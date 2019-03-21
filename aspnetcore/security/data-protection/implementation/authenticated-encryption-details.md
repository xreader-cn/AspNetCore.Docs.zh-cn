---
title: 经过身份验证的加密在 ASP.NET Core 的详细信息
author: rick-anderson
description: 了解实现的身份验证的 ASP.NET Core 数据保护加密的详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208575"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>经过身份验证的加密在 ASP.NET Core 的详细信息

<a name="data-protection-implementation-authenticated-encryption-details"></a>

对 IDataProtector.Protect 的调用都是已验证的加密操作。 保护方法提供保密性和真实性，并绑定到用于此特定 IDataProtector 实例派生自其根 IDataProtectionProvider 用途链。

IDataProtector.Protect 将 byte [] 纯文本参数并生成的字节 [] 受保护的负载，其格式为下面所述。 （此外还有一个扩展方法重载以采用纯文本字符串参数并返回字符串的受保护的负载。 如果使用此 API 的受保护的有效负载格式仍将下面的结构，但它会被[base64url 编码](https://tools.ietf.org/html/rfc4648#section-5)。)

## <a name="protected-payload-format"></a>受保护的有效负载格式

受保护的有效负载格式包含三个主要组件：

* 32 位神奇头标识数据保护系统的版本。

* 128 位密钥 id 标识该密钥用于保护此特定有效负载。

* 受保护负载的其余部分是[特定于此密钥中的加密程序](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)。 在下面的示例中，键表示 AES-256-CBC + HMACSHA256 加密程序，并有效负载被进一步细分，如下所示：
  * 128 位的密钥修饰符。
  * 一个 128 位初始化向量。
  * AES-256-CBC 输出 48 个字节。
  * 一个 HMACSHA256 身份验证标记。

如下图所示的示例受保护的有效负载。

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

从上面的第一个 32 位或 4 个字节的有效负载格式是版本 (09 F0 第 9 频道 F0) 标识的神奇标头

下一步的 128 位或 16 个字节是密钥标识符 (80 9 C 81 0 C 19 66 19 40 95 36 53 F8 AA FF EE 57)

其余部分包含的有效负载和特定于所使用的格式。

> [!WARNING]
> 给定键中受保护的所有负载将都以相同的 20 字节 （不可思议的值，密钥 id） 标头开头。 管理员可以使用用于诊断目的的这一事实来估计时生成一个有效负载。 例如，上述有效负载对应于键 {0c819c80-6619-4019-9536-53f8aaffee57}。 如果检查密钥的存储库后发现，此特定密钥的激活日期为 2015年-01-01 和其到期日期为 2015年-03-01，则非常合理地假定 （如果未被篡改） 在该窗口中，为生成或负载需要一个较小任意一侧种附加因素。
