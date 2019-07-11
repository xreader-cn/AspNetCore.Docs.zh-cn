---
title: 在 ASP.NET Core 的上下文标头
author: rick-anderson
description: 了解 ASP.NET Core 数据保护上下文标头的实现详细信息。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 518423f5df93924d3df144994e4beb1755cd0bfc
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814012"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="83a9e-103">在 ASP.NET Core 的上下文标头</span><span class="sxs-lookup"><span data-stu-id="83a9e-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="83a9e-104">背景和理论</span><span class="sxs-lookup"><span data-stu-id="83a9e-104">Background and theory</span></span>

<span data-ttu-id="83a9e-105">数据保护系统，在"密钥"意味着可以提供的对象进行身份验证的加密服务。</span><span class="sxs-lookup"><span data-stu-id="83a9e-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="83a9e-106">每个键由唯一 id (GUID) 和它所携带算法的信息和 entropic 材料。</span><span class="sxs-lookup"><span data-stu-id="83a9e-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="83a9e-107">其目的是，每个密钥执行唯一平均信息量，但系统不能强制实施的并且我们还需要考虑到开发人员可能会通过修改密钥环中的现有密钥的算法信息手动更改密钥环。</span><span class="sxs-lookup"><span data-stu-id="83a9e-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="83a9e-108">若要实现这种情况下给出了安全要求的数据保护系统有一个的概念[加密灵活性](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/)，它允许安全地跨多个加密算法使用单个 entropic 值。</span><span class="sxs-lookup"><span data-stu-id="83a9e-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="83a9e-109">支持的加密灵活性的大多数系统通过实现包括一些有关算法的有效负载中的标识信息。</span><span class="sxs-lookup"><span data-stu-id="83a9e-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="83a9e-110">算法的 OID 通常是此的良好候选项。</span><span class="sxs-lookup"><span data-stu-id="83a9e-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="83a9e-111">但是，我们遇到的一个问题是有多种方法来指定相同的算法："AES"(技术 CNG) 的托管 Aes、 AesManaged，AesCryptoServiceProvider、 AesCng 和 （如果有特定的参数） 的 RijndaelManaged 类是所有实际相同的操作，而我们需要维护所有这些到正确的 OID 的映射。</span><span class="sxs-lookup"><span data-stu-id="83a9e-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="83a9e-112">如果开发人员想要提供自定义算法 （或甚至另一个实现的 AES ！），他们将需要告诉我们其 OID。</span><span class="sxs-lookup"><span data-stu-id="83a9e-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="83a9e-113">此额外的注册步骤是通知系统配置特别棘手。</span><span class="sxs-lookup"><span data-stu-id="83a9e-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="83a9e-114">回顾一下，我们决定我们已从错误的方向研究这个问题。</span><span class="sxs-lookup"><span data-stu-id="83a9e-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="83a9e-115">OID 告诉您该算法是什么，但我们实际上并不在乎相关信息。</span><span class="sxs-lookup"><span data-stu-id="83a9e-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="83a9e-116">如果我们需要在两个不同的算法中安全地使用单个 entropic 值，它不是我们知道的算法实际上包括所必要的。</span><span class="sxs-lookup"><span data-stu-id="83a9e-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="83a9e-117">我们真正关心的是它们的行为方式。</span><span class="sxs-lookup"><span data-stu-id="83a9e-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="83a9e-118">任何适当的对称块加密算法也是强伪随机排列 (PRP): 修复输入 （键，链接模式中，IV，纯文本） 并且已加密文本输出将与庞大的概率可能得到不同于任何其他对称块加密法提供的相同输入的算法。</span><span class="sxs-lookup"><span data-stu-id="83a9e-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="83a9e-119">同样，任何适当的加密哈希函数也是强伪随机函数 (PRF) 和给定的一组固定的输入相应的输出将绝大程度上是不同于任何其他加密哈希函数。</span><span class="sxs-lookup"><span data-stu-id="83a9e-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="83a9e-120">我们使用这一概念的强 Prp 和 PRFs 构建上下文标头。</span><span class="sxs-lookup"><span data-stu-id="83a9e-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="83a9e-121">此上下文标头实质上是可用作稳定的指纹对中的任何给定的操作，使用的算法，并提供所需的数据保护系统的加密灵活性。</span><span class="sxs-lookup"><span data-stu-id="83a9e-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="83a9e-122">此标头可重现且将在后面的一部分[子项派生过程](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)。</span><span class="sxs-lookup"><span data-stu-id="83a9e-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="83a9e-123">有两种不同方法生成的操作模式的基础算法根据上下文标头。</span><span class="sxs-lookup"><span data-stu-id="83a9e-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="83a9e-124">CBC 模式加密 + HMAC 身份验证</span><span class="sxs-lookup"><span data-stu-id="83a9e-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="83a9e-125">上下文标头包含以下组件：</span><span class="sxs-lookup"><span data-stu-id="83a9e-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="83a9e-126">[16 位]值 00 00，这是一个标记，这意味着"CBC 加密 + HMAC 身份验证"。</span><span class="sxs-lookup"><span data-stu-id="83a9e-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="83a9e-127">[32 位]对称块加密算法的密钥长度 （以字节为单位，big endian）。</span><span class="sxs-lookup"><span data-stu-id="83a9e-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="83a9e-128">[32 位]块大小 （以字节为单位，big endian） 的对称块加密算法。</span><span class="sxs-lookup"><span data-stu-id="83a9e-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="83a9e-129">[32 位]HMAC 算法的密钥长度 （以字节为单位，big endian）。</span><span class="sxs-lookup"><span data-stu-id="83a9e-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="83a9e-130">（当前密钥的大小始终与匹配摘要的大小。）</span><span class="sxs-lookup"><span data-stu-id="83a9e-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="83a9e-131">[32 位]HMAC 算法摘要大小 （以字节为单位，big endian）。</span><span class="sxs-lookup"><span data-stu-id="83a9e-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="83a9e-132">EncCBC (K_E、 IV，"")，这是给定空字符串输入的对称块密码算法的输出位置的 iv 通常是全为零向量。</span><span class="sxs-lookup"><span data-stu-id="83a9e-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="83a9e-133">K_E 构造如下所述。</span><span class="sxs-lookup"><span data-stu-id="83a9e-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="83a9e-134">MAC (K_H，"")，它是给定空字符串输入的 HMAC 算法的输出。</span><span class="sxs-lookup"><span data-stu-id="83a9e-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="83a9e-135">K_H 构造如下所述。</span><span class="sxs-lookup"><span data-stu-id="83a9e-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="83a9e-136">理想情况下，我们可以 K_E 和 K_H 传递所有归零向量。</span><span class="sxs-lookup"><span data-stu-id="83a9e-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="83a9e-137">但是，我们想要避免这种情况其中基础算法检查存在的共享密钥的安全性之前执行任何操作 （值得注意的是 DES 和 3DES），它可以阻止使用简单或可重复的模式类似于所有归零矢量。</span><span class="sxs-lookup"><span data-stu-id="83a9e-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="83a9e-138">相反，我们使用 NIST SP800 108 KDF 计数器模式中 (请参阅[NIST SP800 108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，秒 5.1) 具有长度为零的键、 标签和上下文和作为基础 PRF HMACSHA512。</span><span class="sxs-lookup"><span data-stu-id="83a9e-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="83a9e-139">我们派生 |K_E |+ |K_H |个字节的输出，然后将分解结果为 K_E 和 K_H 本身。</span><span class="sxs-lookup"><span data-stu-id="83a9e-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="83a9e-140">从数学上，这表示，如下所示。</span><span class="sxs-lookup"><span data-stu-id="83a9e-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="83a9e-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="83a9e-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="83a9e-142">示例:AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="83a9e-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="83a9e-143">例如，假设其中对称块加密算法是 AES 192 CBC，验证算法是 HMACSHA256。</span><span class="sxs-lookup"><span data-stu-id="83a9e-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="83a9e-144">系统会生成使用以下步骤的上下文标头。</span><span class="sxs-lookup"><span data-stu-id="83a9e-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="83a9e-145">首先，让 (K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512，key =""，标签 =""，上下文 ="")，其中 |K_E |= 192 位和 |K_H |= 每个指定的算法的 256 位。</span><span class="sxs-lookup"><span data-stu-id="83a9e-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="83a9e-146">这会导致 K_E = 5BB6...21DD 和 K_H = A04A...在下面的示例 00A9:</span><span class="sxs-lookup"><span data-stu-id="83a9e-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="83a9e-147">接下来，计算 Enc_CBC (K_E、 IV，"") 的给定 IV 的 AES-192-CBC = 0 \* 和 K_E 同上。</span><span class="sxs-lookup"><span data-stu-id="83a9e-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="83a9e-148">result := F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="83a9e-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="83a9e-149">接下来，计算 MAC (K_H，"") 的 HMACSHA256 作为上面给出 K_H。</span><span class="sxs-lookup"><span data-stu-id="83a9e-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="83a9e-150">结果: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="83a9e-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="83a9e-151">这会生成下面的完整上下文标头：</span><span class="sxs-lookup"><span data-stu-id="83a9e-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="83a9e-152">此上下文标头是已验证的加密算法对 （AES 192 CBC 加密 + HMACSHA256 验证） 的指纹。</span><span class="sxs-lookup"><span data-stu-id="83a9e-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="83a9e-153">这些组件，如所述[上面](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)是：</span><span class="sxs-lookup"><span data-stu-id="83a9e-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="83a9e-154">标记 (00 00)</span><span class="sxs-lookup"><span data-stu-id="83a9e-154">the marker (00 00)</span></span>

* <span data-ttu-id="83a9e-155">块加密密钥长度 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="83a9e-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="83a9e-156">块密码块大小 (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="83a9e-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="83a9e-157">HMAC 密钥长度 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="83a9e-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="83a9e-158">HMAC 摘要的大小 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="83a9e-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="83a9e-159">块加密法 PRP 输出 (F4 74-DB 6F) 和</span><span class="sxs-lookup"><span data-stu-id="83a9e-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="83a9e-160">HMAC PRF 输出 (D4 79-结束)。</span><span class="sxs-lookup"><span data-stu-id="83a9e-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="83a9e-161">CBC 模式加密 + HMAC 身份验证上下文标头生成而不考虑是否通过 Windows CNG 或托管 SymmetricAlgorithm 和 KeyedHashAlgorithm 类型提供的算法实现相同的方式。</span><span class="sxs-lookup"><span data-stu-id="83a9e-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="83a9e-162">这样，在不同操作系统上运行的应用程序以可靠方式生成相同的上下文标头，即使操作系统之间不同算法的实现。</span><span class="sxs-lookup"><span data-stu-id="83a9e-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="83a9e-163">（在实践中，KeyedHashAlgorithm 不一定是正确的 HMAC。</span><span class="sxs-lookup"><span data-stu-id="83a9e-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="83a9e-164">它可以是任何加密哈希算法类型。）</span><span class="sxs-lookup"><span data-stu-id="83a9e-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="83a9e-165">示例:3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="83a9e-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="83a9e-166">首先，让 (K_E | |K_H) = SP800_108_CTR (prf = HMACSHA512，key =""，标签 =""，上下文 ="")，其中 |K_E |= 192 位和 |K_H |= 每个指定的算法的 160 位。</span><span class="sxs-lookup"><span data-stu-id="83a9e-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="83a9e-167">这会导致 K_E = A219...E2BB 和 K_H = DC4A...在下面的示例 B464:</span><span class="sxs-lookup"><span data-stu-id="83a9e-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="83a9e-168">接下来，计算 Enc_CBC (K_E、 IV，"") 的给定 IV 的 3DES-192-CBC = 0 \* 和 K_E 同上。</span><span class="sxs-lookup"><span data-stu-id="83a9e-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="83a9e-169">结果: = ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="83a9e-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="83a9e-170">接下来，计算 MAC (K_H，"") 作为上面给出 K_H HMACSHA1 的。</span><span class="sxs-lookup"><span data-stu-id="83a9e-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="83a9e-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="83a9e-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="83a9e-172">这将产生所的经过身份验证的指纹的完整上下文标头如下所示的加密算法对 （3DES 192 CBC 加密 + HMACSHA1 验证）：</span><span class="sxs-lookup"><span data-stu-id="83a9e-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="83a9e-173">组件细分，如下所示：</span><span class="sxs-lookup"><span data-stu-id="83a9e-173">The components break down as follows:</span></span>

* <span data-ttu-id="83a9e-174">标记 (00 00)</span><span class="sxs-lookup"><span data-stu-id="83a9e-174">the marker (00 00)</span></span>

* <span data-ttu-id="83a9e-175">块加密密钥长度 (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="83a9e-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="83a9e-176">块密码块大小 (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="83a9e-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="83a9e-177">HMAC 密钥长度 (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="83a9e-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="83a9e-178">HMAC 摘要的大小 (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="83a9e-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="83a9e-179">块加密法 PRP 输出 (AB B1-E1 0E) 和</span><span class="sxs-lookup"><span data-stu-id="83a9e-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="83a9e-180">HMAC PRF 输出 (76 EB-结束)。</span><span class="sxs-lookup"><span data-stu-id="83a9e-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="83a9e-181">Galois/计数器模式加密 + 身份验证</span><span class="sxs-lookup"><span data-stu-id="83a9e-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="83a9e-182">上下文标头包含以下组件：</span><span class="sxs-lookup"><span data-stu-id="83a9e-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="83a9e-183">[16 位]值 00 01，这是一个标记，这意味着"GCM 加密 + 身份验证"。</span><span class="sxs-lookup"><span data-stu-id="83a9e-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="83a9e-184">[32 位]对称块加密算法的密钥长度 （以字节为单位，big endian）。</span><span class="sxs-lookup"><span data-stu-id="83a9e-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="83a9e-185">[32 位]已验证的加密操作期间使用 nonce 大小 （以字节为单位，big endian）。</span><span class="sxs-lookup"><span data-stu-id="83a9e-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="83a9e-186">(我们的系统，此固定为 nonce 大小 = 96 位。)</span><span class="sxs-lookup"><span data-stu-id="83a9e-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="83a9e-187">[32 位]块大小 （以字节为单位，big endian） 的对称块加密算法。</span><span class="sxs-lookup"><span data-stu-id="83a9e-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="83a9e-188">(用于 GCM，这固定的块大小 = 128 位。)</span><span class="sxs-lookup"><span data-stu-id="83a9e-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="83a9e-189">[32 位]身份验证标记大小 （以字节为单位，big endian） 生成的已验证的加密函数。</span><span class="sxs-lookup"><span data-stu-id="83a9e-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="83a9e-190">(我们的系统，此固定为标记大小 = 128 位。)</span><span class="sxs-lookup"><span data-stu-id="83a9e-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="83a9e-191">[128 位]Enc_GCM 的标记 (K_E，nonce，"")，这是给定空字符串输入的对称块密码算法的输出其中 nonce 是 96 位全为零向量。</span><span class="sxs-lookup"><span data-stu-id="83a9e-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="83a9e-192">使用如 CBC 加密 + HMAC 身份验证方案中所示的相同机制派生 K_E。</span><span class="sxs-lookup"><span data-stu-id="83a9e-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="83a9e-193">但是，由于没有 K_H 中起作用，我们实质上是必须 |K_H |= 0 时，该算法将折叠为和窗体下。</span><span class="sxs-lookup"><span data-stu-id="83a9e-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="83a9e-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span><span class="sxs-lookup"><span data-stu-id="83a9e-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="83a9e-195">示例:AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="83a9e-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="83a9e-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span><span class="sxs-lookup"><span data-stu-id="83a9e-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="83a9e-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="83a9e-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="83a9e-198">接下来，计算 Enc_GCM 的身份验证标记 (K_E，nonce，"") 适用于给定 nonce 的 AES-256-GCM = 096 和 K_E 同上。</span><span class="sxs-lookup"><span data-stu-id="83a9e-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="83a9e-199">结果: = E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="83a9e-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="83a9e-200">这会生成下面的完整上下文标头：</span><span class="sxs-lookup"><span data-stu-id="83a9e-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="83a9e-201">组件细分，如下所示：</span><span class="sxs-lookup"><span data-stu-id="83a9e-201">The components break down as follows:</span></span>

* <span data-ttu-id="83a9e-202">标记 (00 01)</span><span class="sxs-lookup"><span data-stu-id="83a9e-202">the marker (00 01)</span></span>

* <span data-ttu-id="83a9e-203">块加密密钥长度 (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="83a9e-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="83a9e-204">nonce 的大小 (00 00 00 0 C)</span><span class="sxs-lookup"><span data-stu-id="83a9e-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="83a9e-205">块密码块大小 (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="83a9e-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="83a9e-206">身份验证标记大小 (00 00 00 10) 和</span><span class="sxs-lookup"><span data-stu-id="83a9e-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="83a9e-207">从运行的块密码的身份验证标记 (E7 DC-结束)。</span><span class="sxs-lookup"><span data-stu-id="83a9e-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
