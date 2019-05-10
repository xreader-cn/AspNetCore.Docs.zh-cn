---
title: 在 ASP.NET Core中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core数据保护 Api 的密码执行哈希。
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896644"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="f8a8c-103">在 ASP.NET Core中的哈希密码</span><span class="sxs-lookup"><span data-stu-id="f8a8c-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="f8a8c-104">数据保护代码库包括包*Microsoft.AspNetCore.Cryptography.KeyDerivation*其中包含加密密钥派生函数。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="f8a8c-105">此包是一个独立组件，并不依赖于数据保护系统的其余部分。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="f8a8c-106">可以完全独立地使用它。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-106">It can be used completely independently.</span></span> <span data-ttu-id="f8a8c-107">源并存的数据保护代码库为方便起见。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="f8a8c-108">包目前提供一种方法`KeyDerivation.Pbkdf2`允许使用密码哈希[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="f8a8c-109">此 API 是非常类似于.NET Framework 的现有[Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)，但具有三个重要的区别：</span><span class="sxs-lookup"><span data-stu-id="f8a8c-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="f8a8c-110">`KeyDerivation.Pbkdf2`方法支持使用多个 PRFs (目前`HMACSHA1`， `HMACSHA256`，和`HMACSHA512`)，而`Rfc2898DeriveBytes`类型仅支持`HMACSHA1`。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="f8a8c-111">`KeyDerivation.Pbkdf2`方法检测到当前操作系统，并尝试选择提供更好的性能，在某些情况下的例程的经过最完美优化的实现。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="f8a8c-112">(在 Windows 8 中，它提供了大约 10 倍的吞吐量`Rfc2898DeriveBytes`。)</span><span class="sxs-lookup"><span data-stu-id="f8a8c-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="f8a8c-113">`KeyDerivation.Pbkdf2`方法需要调用方指定所有参数 （给加盐，PRF 和迭代次数）。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="f8a8c-114">`Rfc2898DeriveBytes`类型提供的这些默认值。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="f8a8c-115">请参阅[源代码](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs)有关 ASP.NET Core 标识`PasswordHasher`类型实际用例。</span><span class="sxs-lookup"><span data-stu-id="f8a8c-115">See the [source code](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
