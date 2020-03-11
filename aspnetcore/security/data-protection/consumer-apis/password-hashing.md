---
title: 在 ASP.NET Core中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core数据保护 Api 的密码执行哈希。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651432"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="2a67f-103">在 ASP.NET Core中的哈希密码</span><span class="sxs-lookup"><span data-stu-id="2a67f-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="2a67f-104">数据保护基本代码包括一个包含加密密钥派生函数的*AspNetCore 密钥派生*。</span><span class="sxs-lookup"><span data-stu-id="2a67f-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="2a67f-105">此包是一个独立的组件，与数据保护系统的其余部分没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="2a67f-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="2a67f-106">它可以完全独立地使用。</span><span class="sxs-lookup"><span data-stu-id="2a67f-106">It can be used completely independently.</span></span> <span data-ttu-id="2a67f-107">为了方便起见，源与数据保护代码库并存。</span><span class="sxs-lookup"><span data-stu-id="2a67f-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="2a67f-108">包当前提供了一个允许使用[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)对密码进行哈希处理 `KeyDerivation.Pbkdf2` 方法。</span><span class="sxs-lookup"><span data-stu-id="2a67f-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="2a67f-109">此 API 与 .NET Framework 现有的[Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常相似，但有三个重要的区别：</span><span class="sxs-lookup"><span data-stu-id="2a67f-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="2a67f-110">`KeyDerivation.Pbkdf2` 方法支持使用多个 PRFs （当前 `HMACSHA1`、`HMACSHA256`和 `HMACSHA512`），而 `Rfc2898DeriveBytes` 类型仅支持 `HMACSHA1`。</span><span class="sxs-lookup"><span data-stu-id="2a67f-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="2a67f-111">`KeyDerivation.Pbkdf2` 方法将检测当前操作系统，并尝试选择最适合的例程实现，在某些情况下提供更好的性能。</span><span class="sxs-lookup"><span data-stu-id="2a67f-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="2a67f-112">（在 Windows 8 中，它提供 `Rfc2898DeriveBytes`吞吐量的10倍。）</span><span class="sxs-lookup"><span data-stu-id="2a67f-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="2a67f-113">`KeyDerivation.Pbkdf2` 方法要求调用方指定所有参数（salt、PRF 和迭代计数）。</span><span class="sxs-lookup"><span data-stu-id="2a67f-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="2a67f-114">`Rfc2898DeriveBytes` 类型为这些值提供默认值。</span><span class="sxs-lookup"><span data-stu-id="2a67f-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="2a67f-115">有关实际用例，请参阅 ASP.NET Core 标识的 `PasswordHasher` 类型的[源代码](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs)。</span><span class="sxs-lookup"><span data-stu-id="2a67f-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
