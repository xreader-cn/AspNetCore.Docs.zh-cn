---
title: 在 ASP.NET Core 的上下文标头
author: rick-anderson
description: 了解 ASP.NET Core 数据保护上下文标头的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2b8fd594672bf623d38bfae90d05a984f92ce6a3
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087555"
---
# <a name="context-headers-in-aspnet-core"></a>在 ASP.NET Core 的上下文标头

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>背景和理论

数据保护系统，在"密钥"意味着可以提供的对象进行身份验证的加密服务。 每个键由唯一 id (GUID) 和它所携带算法的信息和 entropic 材料。 其目的是，每个密钥执行唯一平均信息量，但系统不能强制实施的并且我们还需要考虑到开发人员可能会通过修改密钥环中的现有密钥的算法信息手动更改密钥环。 若要实现这种情况下给出了安全要求的数据保护系统有一个的概念[加密灵活性](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/)，它允许安全地跨多个加密算法使用单个 entropic 值。

支持的加密灵活性的大多数系统通过实现包括一些有关算法的有效负载中的标识信息。 算法的 OID 通常是此的良好候选项。 但是，我们遇到的一个问题是有多种方法来指定相同的算法："AES"(技术 CNG) 的托管 Aes、 AesManaged，AesCryptoServiceProvider、 AesCng 和 （如果有特定的参数） 的 RijndaelManaged 类是所有实际相同的操作，而我们需要维护所有这些到正确的 OID 的映射。 如果开发人员想要提供自定义算法 （或甚至另一个实现的 AES ！），他们将需要告诉我们其 OID。 此额外的注册步骤是通知系统配置特别棘手。

回顾一下，我们决定我们已从错误的方向研究这个问题。 OID 告诉您该算法是什么，但我们实际上并不在乎相关信息。 如果我们需要在两个不同的算法中安全地使用单个 entropic 值，它不是我们知道的算法实际上包括所必要的。 我们真正关心的是它们的行为方式。 任何适当的对称块加密算法也是强伪随机排列 (PRP): 修复输入 （键，链接模式中，IV，纯文本） 并且已加密文本输出将与庞大的概率可能得到不同于任何其他对称块加密法提供的相同输入的算法。 同样，任何适当的加密哈希函数也是强伪随机函数 (PRF) 和给定的一组固定的输入相应的输出将绝大程度上是不同于任何其他加密哈希函数。

我们使用这一概念的强 Prp 和 PRFs 构建上下文标头。 此上下文标头实质上是可用作稳定的指纹对中的任何给定的操作，使用的算法，并提供所需的数据保护系统的加密灵活性。 此标头可重现且将在后面的一部分[子项派生过程](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)。 有两种不同方法生成的操作模式的基础算法根据上下文标头。

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC 模式加密 + HMAC 身份验证

<a name="data-protection-implementation-context-headers-cbc-components"></a>

上下文标头包含以下组件：

* [16 位]值 00 00，这是一个标记，这意味着"CBC 加密 + HMAC 身份验证"。

* [32 位]对称块加密算法的密钥长度 （以字节为单位，big endian）。

* [32 位]块大小 （以字节为单位，big endian） 的对称块加密算法。

* [32 位]HMAC 算法的密钥长度 （以字节为单位，big endian）。 （当前密钥的大小始终与匹配摘要的大小。）

* [32 位]HMAC 算法摘要大小 （以字节为单位，big endian）。

* EncCBC (K_E、 IV，"")，这是给定空字符串输入的对称块密码算法的输出位置的 iv 通常是全为零向量。 K_E 构造如下所述。

* MAC (K_H，"")，它是给定空字符串输入的 HMAC 算法的输出。 K_H 构造如下所述。

理想情况下，我们可以 K_E 和 K_H 传递所有归零向量。 但是，我们想要避免这种情况其中基础算法检查存在的共享密钥的安全性之前执行任何操作 （值得注意的是 DES 和 3DES），它可以阻止使用简单或可重复的模式类似于所有归零矢量。

相反，我们使用 NIST SP800 108 KDF 计数器模式中 (请参阅[NIST SP800 108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，秒 5.1) 具有长度为零的键、 标签和上下文和作为基础 PRF HMACSHA512。 我们派生 |K_E |+ |K_H |个字节的输出，然后将分解结果为 K_E 和 K_H 本身。 从数学上，这表示，如下所示。

( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>示例:AES-192-CBC + HMACSHA256

例如，假设其中对称块加密算法是 AES 192 CBC，验证算法是 HMACSHA256。 系统会生成使用以下步骤的上下文标头。

首先，让 (K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512，key =""，标签 =""，上下文 ="")，其中 |K_E |= 192 位和 |K_H |= 每个指定的算法的 256 位。 这会导致 K_E = 5BB6...21DD 和 K_H = A04A...在下面的示例 00A9:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

接下来，计算 Enc_CBC (K_E、 IV，"") 的给定 IV 的 AES-192-CBC = 0 * 和 K_E 同上。

result := F474B1872B3B53E4721DE19C0841DB6F

接下来，计算 MAC (K_H，"") 的 HMACSHA256 作为上面给出 K_H。

结果: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

这会生成下面的完整上下文标头：

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

此上下文标头是已验证的加密算法对 （AES 192 CBC 加密 + HMACSHA256 验证） 的指纹。 这些组件，如所述[上面](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)是：

* 标记 (00 00)

* 块加密密钥长度 (00 00 00 18)

* 块密码块大小 (00 00 00 10)

* HMAC 密钥长度 (00 00 00 20)

* HMAC 摘要的大小 (00 00 00 20)

* 块加密法 PRP 输出 (F4 74-DB 6F) 和

* HMAC PRF 输出 (D4 79-结束)。

> [!NOTE]
> CBC 模式加密 + HMAC 身份验证上下文标头生成而不考虑是否通过 Windows CNG 或托管 SymmetricAlgorithm 和 KeyedHashAlgorithm 类型提供的算法实现相同的方式。 这样，在不同操作系统上运行的应用程序以可靠方式生成相同的上下文标头，即使操作系统之间不同算法的实现。 （在实践中，KeyedHashAlgorithm 不一定是正确的 HMAC。 它可以是任何加密哈希算法类型。）

### <a name="example-3des-192-cbc--hmacsha1"></a>示例:3DES-192-CBC + HMACSHA1

首先，让 (K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512，key =""，标签 =""，上下文 ="")，其中 |K_E |= 192 位和 |K_H |= 每个指定的算法的 160 位。 这会导致 K_E = A219...E2BB 和 K_H = DC4A...在下面的示例 B464:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

接下来，计算 Enc_CBC (K_E、 IV，"") 的给定 IV 的 3DES-192-CBC = 0 * 和 K_E 同上。

结果: = ABB100F81E53E10E

接下来，计算 MAC (K_H，"") 作为上面给出 K_H HMACSHA1 的。

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

这将产生所的经过身份验证的指纹的完整上下文标头如下所示的加密算法对 （3DES 192 CBC 加密 + HMACSHA1 验证）：

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

组件细分，如下所示：

* 标记 (00 00)

* 块加密密钥长度 (00 00 00 18)

* 块密码块大小 (00 00 00 08)

* HMAC 密钥长度 (00 00 00 14)

* HMAC 摘要的大小 (00 00 00 14)

* 块加密法 PRP 输出 (AB B1-E1 0E) 和

* HMAC PRF 输出 (76 EB-结束)。

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/计数器模式加密 + 身份验证

上下文标头包含以下组件：

* [16 位]值 00 01，这是一个标记，这意味着"GCM 加密 + 身份验证"。

* [32 位]对称块加密算法的密钥长度 （以字节为单位，big endian）。

* [32 位]已验证的加密操作期间使用 nonce 大小 （以字节为单位，big endian）。 (我们的系统，此固定为 nonce 大小 = 96 位。)

* [32 位]块大小 （以字节为单位，big endian） 的对称块加密算法。 (用于 GCM，这固定的块大小 = 128 位。)

* [32 位]身份验证标记大小 （以字节为单位，big endian） 生成的已验证的加密函数。 (我们的系统，此固定为标记大小 = 128 位。)

* [128 位]Enc_GCM 的标记 (K_E，nonce，"")，这是给定空字符串输入的对称块密码算法的输出其中 nonce 是 96 位全为零向量。

使用如 CBC 加密 + HMAC 身份验证方案中所示的相同机制派生 K_E。 但是，由于没有 K_H 中起作用，我们实质上是必须 |K_H |= 0 时，该算法将折叠为和窗体下。

K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-256-gcm"></a>示例:AES-256-GCM

First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.

K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

接下来，计算 Enc_GCM 的身份验证标记 (K_E，nonce，"") 适用于给定 nonce 的 AES-256-GCM = 096 和 K_E 同上。

结果: = E7DCCE66DF855A323A6BB7BD7A59BE45

这会生成下面的完整上下文标头：

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

组件细分，如下所示：

* 标记 (00 01)

* 块加密密钥长度 (00 00 00 20)

* nonce 的大小 (00 00 00 0 C)

* 块密码块大小 (00 00 00 10)

* 身份验证标记大小 (00 00 00 10) 和

* 从运行的块密码的身份验证标记 (E7 DC-结束)。
