---
title: 经过身份验证的加密在 ASP.NET Core 的详细信息
author: rick-anderson
description: 了解实现的身份验证的 ASP.NET Core 数据保护加密的详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655002"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="84235-103">经过身份验证的加密在 ASP.NET Core 的详细信息</span><span class="sxs-lookup"><span data-stu-id="84235-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="84235-104">对 IDataProtector 的调用是经过身份验证的加密操作。</span><span class="sxs-lookup"><span data-stu-id="84235-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="84235-105">保护方法同时提供机密性和真实性，并与用于从其根 IDataProtectionProvider 派生此特定 IDataProtector 实例的用途链相关联。</span><span class="sxs-lookup"><span data-stu-id="84235-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="84235-106">IDataProtector 使用 byte [] 纯文本参数，并生成一个 byte [] 受保护的负载，其格式如下所述。</span><span class="sxs-lookup"><span data-stu-id="84235-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="84235-107">（还有一个扩展方法重载，它采用字符串纯文本参数并返回一个字符串受保护的负载。</span><span class="sxs-lookup"><span data-stu-id="84235-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="84235-108">如果使用此 API，受保护的负载格式仍将具有下面的结构，但将被[base64url 编码](https://tools.ietf.org/html/rfc4648#section-5)。）</span><span class="sxs-lookup"><span data-stu-id="84235-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="84235-109">受保护的负载格式</span><span class="sxs-lookup"><span data-stu-id="84235-109">Protected payload format</span></span>

<span data-ttu-id="84235-110">受保护的负载格式由三个主要组件组成：</span><span class="sxs-lookup"><span data-stu-id="84235-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="84235-111">用于标识数据保护系统版本的32位幻标头。</span><span class="sxs-lookup"><span data-stu-id="84235-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="84235-112">一个128位密钥 id，用于标识用于保护此特定有效负载的密钥。</span><span class="sxs-lookup"><span data-stu-id="84235-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="84235-113">受保护负载的其余部分[特定于此密钥封装的加密程序](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)。</span><span class="sxs-lookup"><span data-stu-id="84235-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="84235-114">在下面的示例中，键表示 AES-256-CBC + HMACSHA256 加密器，负载进一步细分，如下所示：</span><span class="sxs-lookup"><span data-stu-id="84235-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="84235-115">128位密钥修饰符。</span><span class="sxs-lookup"><span data-stu-id="84235-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="84235-116">128位初始化向量。</span><span class="sxs-lookup"><span data-stu-id="84235-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="84235-117">48字节的 AES-256-CBC 输出。</span><span class="sxs-lookup"><span data-stu-id="84235-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="84235-118">HMACSHA256 authentication 标记。</span><span class="sxs-lookup"><span data-stu-id="84235-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="84235-119">下面说明了一个受保护的有效负载示例。</span><span class="sxs-lookup"><span data-stu-id="84235-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="84235-120">从前32位以上的负载格式，或者4个字节是标识版本的幻标头（09 F0 C9 F0）</span><span class="sxs-lookup"><span data-stu-id="84235-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="84235-121">接下来的128位或16字节是密钥标识符（80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57）</span><span class="sxs-lookup"><span data-stu-id="84235-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="84235-122">余数包含负载并且特定于所使用的格式。</span><span class="sxs-lookup"><span data-stu-id="84235-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="84235-123">所有受指定密钥保护的有效负载将以相同的20字节（幻值，key id）标头开头。</span><span class="sxs-lookup"><span data-stu-id="84235-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="84235-124">管理员可以将此事实用于诊断目的，以大致了解生成负载的时间。</span><span class="sxs-lookup"><span data-stu-id="84235-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="84235-125">例如，上面的负载对应于键 {0c819c80-6619-4019-9536-53f8aaffee57}。</span><span class="sxs-lookup"><span data-stu-id="84235-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="84235-126">如果在检查密钥存储库之后发现此特定密钥的激活日期为2015-01-01，并且其到期日期为2015-03-01，则必须假定在该时段内生成了有效负载（如果不被篡改），并提供或花费少量两侧的奶油因素。</span><span class="sxs-lookup"><span data-stu-id="84235-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
