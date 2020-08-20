---
title: ASP.NET Core 中的上下文标题
author: rick-anderson
description: 了解 ASP.NET Core 数据保护上下文标题的实现细节。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2f07db4b7d8bca9f64aee5d60e88fc92dc8965eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633703"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="a39e8-103">ASP.NET Core 中的上下文标题</span><span class="sxs-lookup"><span data-stu-id="a39e8-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="a39e8-104">背景和理论</span><span class="sxs-lookup"><span data-stu-id="a39e8-104">Background and theory</span></span>

<span data-ttu-id="a39e8-105">在数据保护系统中，"密钥" 是指可提供经过身份验证的加密服务的对象。</span><span class="sxs-lookup"><span data-stu-id="a39e8-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="a39e8-106">每个密钥都是由 GUID)  (唯一 id 标识的，它附带了它的算法信息和 entropic 材料。</span><span class="sxs-lookup"><span data-stu-id="a39e8-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="a39e8-107">它的目的是，每个密钥都具有唯一的平均信息量，但系统不能强制实施这一点，并且我们还需要考虑到通过修改密钥环中现有密钥的算法信息来手动更改密钥环的开发人员。</span><span class="sxs-lookup"><span data-stu-id="a39e8-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="a39e8-108">为实现安全要求，在这种情况下，数据保护系统具有 [加密灵活性](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)，这允许使用跨多个加密算法的单个 entropic 值安全地使用。</span><span class="sxs-lookup"><span data-stu-id="a39e8-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="a39e8-109">大多数支持加密灵活性的系统通过在有效负载中包含有关算法的一些识别信息来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="a39e8-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="a39e8-110">通常，该算法的 OID 是一个不错的候选项。</span><span class="sxs-lookup"><span data-stu-id="a39e8-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="a39e8-111">但是，我们遇到的一个问题是，有多种方法可以指定相同的算法： "AES" (CNG) ，托管的 Aes、AesManaged、AesCryptoServiceProvider、AesCng 和 RijndaelManaged (给定特定参数的特定参数) 类实际上是相同的，因此，我们需要维护所有这些参数到正确 OID 的映射。</span><span class="sxs-lookup"><span data-stu-id="a39e8-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="a39e8-112">如果开发人员想要提供自定义算法 (甚至 AES！ ) 的其他实现，则他们必须告诉我们其 OID。</span><span class="sxs-lookup"><span data-stu-id="a39e8-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="a39e8-113">此额外注册步骤使系统配置特别令人头痛。</span><span class="sxs-lookup"><span data-stu-id="a39e8-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="a39e8-114">再回顾一步，我们决定我们从错误的方向接近问题。</span><span class="sxs-lookup"><span data-stu-id="a39e8-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="a39e8-115">OID 告诉您算法是什么，但我们并不真正关心这一点。</span><span class="sxs-lookup"><span data-stu-id="a39e8-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="a39e8-116">如果需要以两个不同的算法安全地使用单个 entropic 值，我们不需要知道算法的实际含义。</span><span class="sxs-lookup"><span data-stu-id="a39e8-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="a39e8-117">我们真正关心的是它们的行为方式。</span><span class="sxs-lookup"><span data-stu-id="a39e8-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="a39e8-118">任何适当的对称块加密算法也是一种功能强大的伪随机排列 (PRP) ：修复输入 (密钥、链接模式、IV、纯文本) 和密码文本输出与任何其他对称块加密算法相比，给定的输入相同。</span><span class="sxs-lookup"><span data-stu-id="a39e8-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="a39e8-119">同样，任何适当的键控哈希函数也是 (PRF) 的强伪随机函数，并且给定固定输入集，其输出将充分与任何其他键控哈希函数不同。</span><span class="sxs-lookup"><span data-stu-id="a39e8-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="a39e8-120">我们使用强 PRPs 和 PRFs 这一概念来构建上下文标头。</span><span class="sxs-lookup"><span data-stu-id="a39e8-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="a39e8-121">此上下文标头本质上可充当用于任何给定操作的算法的稳定指纹，并提供数据保护系统所需的加密灵活性。</span><span class="sxs-lookup"><span data-stu-id="a39e8-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="a39e8-122">此标头可重复使用，稍后将用作 [子项派生过程](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)的一部分。</span><span class="sxs-lookup"><span data-stu-id="a39e8-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="a39e8-123">可以通过两种不同的方式生成上下文标题，具体取决于基础算法的操作模式。</span><span class="sxs-lookup"><span data-stu-id="a39e8-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="a39e8-124">CBC 模式加密 + HMAC 身份验证</span><span class="sxs-lookup"><span data-stu-id="a39e8-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="a39e8-125">上下文标头包含以下组件：</span><span class="sxs-lookup"><span data-stu-id="a39e8-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="a39e8-126">[16 位]值 00 00，它是一个标记，表示 "CBC 加密 + HMAC 身份验证"。</span><span class="sxs-lookup"><span data-stu-id="a39e8-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="a39e8-127">[32 位]对称块加密算法的密钥长度 (以字节为单位，大字节序) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="a39e8-128">[32 位]对称块密码算法的块大小 (以字节为单位，大字节序) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="a39e8-129">[32 位]HMAC 算法的密钥长度 (以字节为单位，大字节序) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="a39e8-130"> (当前密钥大小始终与摘要大小匹配。 ) </span><span class="sxs-lookup"><span data-stu-id="a39e8-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="a39e8-131">[32 位]HMAC 算法的摘要大小 (（以字节为单位），大字节序) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="a39e8-132">`EncCBC(K_E, IV, "")`，为对称块加密算法的输出，提供空字符串输入，其中 IV 为全零向量。</span><span class="sxs-lookup"><span data-stu-id="a39e8-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="a39e8-133">`K_E`下面介绍了的构造。</span><span class="sxs-lookup"><span data-stu-id="a39e8-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="a39e8-134">`MAC(K_H, "")`，它是给定空字符串输入的 HMAC 算法的输出。</span><span class="sxs-lookup"><span data-stu-id="a39e8-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="a39e8-135">`K_H`下面介绍了的构造。</span><span class="sxs-lookup"><span data-stu-id="a39e8-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="a39e8-136">理想情况下，我们可以将所有-zero 向量传递给 `K_E` 和 `K_H` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="a39e8-137">但是，我们想要避免在执行任何 (操作之前，基础算法检查是否存在弱密钥，而这种情况会导致 DES 和 3DES) ，而不是使用简单或可重复的模式，例如全零向量。</span><span class="sxs-lookup"><span data-stu-id="a39e8-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="a39e8-138">相反，我们在计数器模式下使用 NIST SP800-108 KDF (参阅 [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，5.1) ，其中长度为零、标签和上下文，HMACSHA512 作为基础 PRF。</span><span class="sxs-lookup"><span data-stu-id="a39e8-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="a39e8-139">我们派生 `| K_E | + | K_H |` 输出字节，然后将结果分解为 `K_E` `K_H` 自身。</span><span class="sxs-lookup"><span data-stu-id="a39e8-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="a39e8-140">从数学上来说，这种情况如下所示。</span><span class="sxs-lookup"><span data-stu-id="a39e8-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="a39e8-141">示例： AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="a39e8-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="a39e8-142">作为示例，请考虑对称块加密算法为 AES-192-CBC 并且验证算法为 HMACSHA256 的情况。</span><span class="sxs-lookup"><span data-stu-id="a39e8-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="a39e8-143">系统将使用以下步骤生成上下文标头。</span><span class="sxs-lookup"><span data-stu-id="a39e8-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="a39e8-144">首先，让 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` ， `| K_E | = 192 bits` `| K_H | = 256 bits` 根据指定的算法和位置。</span><span class="sxs-lookup"><span data-stu-id="a39e8-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="a39e8-145">这会导致 `K_E = 5BB6..21DD` 和 `K_H = A04A..00A9` 在以下示例中：</span><span class="sxs-lookup"><span data-stu-id="a39e8-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="a39e8-146">接下来，计算 `Enc_CBC (K_E, IV, "")` 给定 `IV = 0*` 和更高版本的 AES-192-CBC `K_E` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="a39e8-147">接下来，计算 `MAC(K_H, "")` HMACSHA256 给定的 `K_H` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="a39e8-148">这会生成以下完整的上下文标头：</span><span class="sxs-lookup"><span data-stu-id="a39e8-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="a39e8-149">此上下文标头是经过身份验证的加密算法对的指纹 (AES-192-CBC encryption + HMACSHA256 验证) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="a39e8-150">如上所述，组件如下所[述：](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)</span><span class="sxs-lookup"><span data-stu-id="a39e8-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="a39e8-151">标记 `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="a39e8-152">块加密密钥长度 `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="a39e8-153">块加密块大小 `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="a39e8-154">HMAC 密钥长度 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="a39e8-155">HMAC 摘要大小 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="a39e8-156">块密码 PRP 输出 `(F4 74 - DB 6F)` 和</span><span class="sxs-lookup"><span data-stu-id="a39e8-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="a39e8-157">HMAC PRF 输出 `(D4 79 - end)` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="a39e8-158">无论算法实现由 Windows CNG 还是由托管的 System.security.cryptography.symmetricalgorithm 和 KeyedHashAlgorithm 类型提供，CBC 模式加密 + HMAC 身份验证上下文标题都采用相同的方式生成。</span><span class="sxs-lookup"><span data-stu-id="a39e8-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="a39e8-159">这使得在不同操作系统上运行的应用程序可以可靠地生成相同的上下文标头，即使这些算法的实现在操作系统之间有所不同。</span><span class="sxs-lookup"><span data-stu-id="a39e8-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="a39e8-160"> (实际上，KeyedHashAlgorithm 不必是正确的 HMAC。</span><span class="sxs-lookup"><span data-stu-id="a39e8-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="a39e8-161">它可以是任何密钥哈希算法类型。 ) </span><span class="sxs-lookup"><span data-stu-id="a39e8-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="a39e8-162">示例： 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="a39e8-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="a39e8-163">首先，让 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` ， `| K_E | = 192 bits` `| K_H | = 160 bits` 根据指定的算法和位置。</span><span class="sxs-lookup"><span data-stu-id="a39e8-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="a39e8-164">这会导致 `K_E = A219..E2BB` 和 `K_H = DC4A..B464` 在以下示例中：</span><span class="sxs-lookup"><span data-stu-id="a39e8-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="a39e8-165">接下来，计算 `Enc_CBC (K_E, IV, "")` 给定 `IV = 0*` 和更高版本的 3des-192-CBC `K_E` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="a39e8-166">接下来，计算 `MAC(K_H, "")` HMACSHA1 给定的 `K_H` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="a39e8-167">这将生成完整的上下文标头，该标头是经过身份验证的加密算法对的指纹 (3DES-192-CBC encryption + HMACSHA1 验证) ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a39e8-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="a39e8-168">组件将按如下方式进行分解：</span><span class="sxs-lookup"><span data-stu-id="a39e8-168">The components break down as follows:</span></span>

* <span data-ttu-id="a39e8-169">标记 `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="a39e8-170">块加密密钥长度 `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="a39e8-171">块加密块大小 `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="a39e8-172">HMAC 密钥长度 `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="a39e8-173">HMAC 摘要大小 `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="a39e8-174">块密码 PRP 输出 `(AB B1 - E1 0E)` 和</span><span class="sxs-lookup"><span data-stu-id="a39e8-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="a39e8-175">HMAC PRF 输出 `(76 EB - end)` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="a39e8-176">Galois/Counter 模式加密 + 身份验证</span><span class="sxs-lookup"><span data-stu-id="a39e8-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="a39e8-177">上下文标头包含以下组件：</span><span class="sxs-lookup"><span data-stu-id="a39e8-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="a39e8-178">[16 位]值 00 01，它是一个标记，表示 "GCM 加密 + 身份验证"。</span><span class="sxs-lookup"><span data-stu-id="a39e8-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="a39e8-179">[32 位]对称块加密算法的密钥长度 (以字节为单位，大字节序) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="a39e8-180">[32 位]在经过身份验证的加密操作期间) ，nonce 大小 (以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="a39e8-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="a39e8-181">对于我们的系统 (，这是在 nonce size = 96 位上固定的。 ) </span><span class="sxs-lookup"><span data-stu-id="a39e8-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="a39e8-182">[32 位]对称块密码算法的块大小 (以字节为单位，大字节序) 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="a39e8-183"> (GCM，这是在块大小 = 128 位固定的。 ) </span><span class="sxs-lookup"><span data-stu-id="a39e8-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="a39e8-184">[32 位]身份验证标记大小 () （以字节为单位），由经过身份验证的加密功能生成。</span><span class="sxs-lookup"><span data-stu-id="a39e8-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="a39e8-185">对于我们的系统 (，这是在 tag size = 128 位上固定的。 ) </span><span class="sxs-lookup"><span data-stu-id="a39e8-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="a39e8-186">[128 位]的标记 `Enc_GCM (K_E, nonce, "")` ，它是对称块加密算法的输出，提供的是空字符串输入，其中 nonce 为96位全零向量。</span><span class="sxs-lookup"><span data-stu-id="a39e8-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="a39e8-187">`K_E` 使用与在 CBC 加密 + HMAC 身份验证方案中相同的机制来派生。</span><span class="sxs-lookup"><span data-stu-id="a39e8-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="a39e8-188">不过，由于这里没有任何内容 `K_H` ，因此我们实质上具有 `| K_H | = 0` ，算法折叠到下面的窗体中。</span><span class="sxs-lookup"><span data-stu-id="a39e8-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="a39e8-189">示例： AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="a39e8-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="a39e8-190">首先，让吧 `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 256 bits` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="a39e8-191">接下来，计算 `Enc_GCM (K_E, nonce, "")` 给定 `nonce = 096` 和更高版本的 256-GCM 的身份验证标记 `K_E` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="a39e8-192">这会生成以下完整的上下文标头：</span><span class="sxs-lookup"><span data-stu-id="a39e8-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="a39e8-193">组件将按如下方式进行分解：</span><span class="sxs-lookup"><span data-stu-id="a39e8-193">The components break down as follows:</span></span>

* <span data-ttu-id="a39e8-194">标记 `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="a39e8-195">块加密密钥长度 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="a39e8-196">nonce 大小 `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="a39e8-197">块加密块大小 `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="a39e8-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="a39e8-198">身份验证标记大小 `(00 00 00 10)` 和</span><span class="sxs-lookup"><span data-stu-id="a39e8-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="a39e8-199">运行块密码的身份验证标记 `(E7 DC - end)` 。</span><span class="sxs-lookup"><span data-stu-id="a39e8-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
