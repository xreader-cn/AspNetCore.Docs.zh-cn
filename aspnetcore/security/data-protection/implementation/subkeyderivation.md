---
title: 子项派生和 ASP.NET Core 中的经过身份验证的加密
author: rick-anderson
description: 了解实现详细信息的 ASP.NET Core 数据保护子项派生和身份验证加密。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 37e7b01700e8a6b755b5ed16a9d7d75a9eeb970e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891834"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>子项派生和 ASP.NET Core 中的经过身份验证的加密

<a name="data-protection-implementation-subkey-derivation"></a>

密钥环中的大多数键将包含某种形式的平均信息量，也将具有算法信息指出"CBC 模式加密 + HMAC 验证"或"GCM 加密 + 验证"。 在这些情况下，我们称此密钥的主密钥密钥材料 （或公里） 为嵌入的平均信息量和密钥派生函数派生的密钥将用于实际加密操作，我们会执行。

> [!NOTE]
> 键是抽象的并自定义实现可能不会按如下所示。 如果密钥提供了自己的实现`IAuthenticatedEncryptor`而不是使用某个内置工厂，在本部分中描述的机制不再适用。

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>更多的已经过身份验证的数据和子项派生

`IAuthenticatedEncryptor`接口充当所有经过身份验证的加密操作的核心接口。 其`Encrypt`方法采用两个缓冲区： 纯文本和 additionalAuthenticatedData (AAD)。 纯文本内容流保持不变的调用`IDataProtector.Protect`，但 AAD 由系统生成和三个组件组成：

1. 32 位神奇标头 09 F0 第 9 频道 F0 标识此版本的数据保护系统。

2. 128 位密钥 id。

3. 长度可变的字符串名称来形成创建用途链`IDataProtector`的执行此操作。

因为 AAD 是为所有三个组件的元组唯一的我们可以使用它从 KM 派生新的密钥，而不是使用密钥主机本身中所有的我们的加密操作。 每次调用`IAuthenticatedEncryptor.Encrypt`，发生以下密钥派生过程：

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

在这里，我们正在呼叫 NIST SP800 108 KDF 计数器模式中 (请参阅[NIST SP800 108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，秒 5.1) 使用以下参数：

* 密钥派生密钥 (KDK) = K_M

* PRF = HMACSHA512

* 标签 = additionalAuthenticatedData

* context = contextHeader || keyModifier

上下文标头的可变长度是并实质上是充当我们要为其派生 K_E 和 K_H 的算法的指纹。 键修饰符是随机生成的每次调用一个 128 位字符串`Encrypt`并提供以确保能通过庞大的概率 KE 和 KH 是唯一的此特定身份验证加密操作时，即使所有其他输入 KDF 是常量。

CBC 模式加密 + HMAC 验证操作 |K_E |是对称块加密密钥的长度和 |K_H |是的 HMAC 例程摘要的大小。 GCM 加密 + 验证操作 |K_H |= 0。

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC 模式加密 + HMAC 验证

K_E 生成后通过上述机制，我们生成的随机初始化向量，并运行对称块加密算法来加密纯文本。 然后通过使用密钥 K_H 以生成 MAC 初始化 HMAC 例程将初始化向量和已加密文本运行 下面以图形方式表示此过程，并返回值。

![CBC 模式进程，且返回](subkeyderivation/_static/cbcprocess.png)

*output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))*

> [!NOTE]
> `IDataProtector.Protect`实现会[前面添加的 magic 标头和密钥 id](xref:security/data-protection/implementation/authenticated-encryption-details)到返回给调用方之前的输出。 因为神奇的标头和密钥 id 是隐式的一部分[AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)，因为键修饰符作为输入发送给 KDF，这意味着 MAC 进行身份验证的最终返回负载的每个单字节

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/计数器模式加密 + 验证

K_E 生成后通过上述机制，我们将生成随机的 96 位 nonce 和运行对称块加密算法来加密纯文本并生成 128 位的身份验证标记。

![GCM 模式进程，且返回](subkeyderivation/_static/galoisprocess.png)

*输出: = keyModifier | |nonce | |E_gcm （K_E，nonce，数据） | |authTag*

> [!NOTE]
> 即使 GCM 以本机方式支持 AAD 的概念，我们要仍输入 AAD 仅对原始 KDF，从而选择启用其 AAD 参数向 GCM 传递空字符串。 这样做的原因有两层含义。 首先，[以支持灵活性](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers)我们永远不会想要直接将 K_M 用作加密密钥。 此外，GCM 对施加非常严格的唯一性要求其输入。 GCM 加密例程是不断调用对两个或更多不同的概率的具有相同 （键，nonce） 的输入数据集对不能超过 2 ^32。 如果我们修复 K_E 我们无法执行多个 2 ^32 加密操作之前我们落入最常见的 2 ^-32 限制。 这可能看起来好像非常大量的操作，但高流量 web 服务器可在单纯天内，也在两个注册表项通常的生存期内经历 40 亿个请求。 若要保持符合性对 2 ^-32 概率限制，我们继续使用 128 位密钥修饰符和 96 位 nonce，大大扩展了任何给定 K_M 的可用操作计数。 我们的设计的简单性共享 CBC 和 GCM 操作之间的 KDF 代码路径，并且由于 AAD 已被视为 KDF 中没有无需将其转发到 GCM 例程。
