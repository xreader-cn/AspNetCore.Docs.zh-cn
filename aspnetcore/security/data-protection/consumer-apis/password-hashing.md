---
title: ASP.NET Core 中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 为密码进行哈希处理。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: f7d15cab463972d9c0fff52b645be454865ce2ca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408950"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="3c333-103">ASP.NET Core 中的哈希密码</span><span class="sxs-lookup"><span data-stu-id="3c333-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="3c333-104">数据保护基本代码包括一个包含加密密钥派生函数的*AspNetCore 密钥派生*。</span><span class="sxs-lookup"><span data-stu-id="3c333-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="3c333-105">此包是一个独立的组件，与数据保护系统的其余部分没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="3c333-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="3c333-106">它可以完全独立地使用。</span><span class="sxs-lookup"><span data-stu-id="3c333-106">It can be used completely independently.</span></span> <span data-ttu-id="3c333-107">为了方便起见，源与数据保护代码库并存。</span><span class="sxs-lookup"><span data-stu-id="3c333-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="3c333-108">包当前提供了一个方法 `KeyDerivation.Pbkdf2` ，该方法允许使用[PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)对密码进行哈希处理。</span><span class="sxs-lookup"><span data-stu-id="3c333-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="3c333-109">此 API 与 .NET Framework 现有的[Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常相似，但有三个重要的区别：</span><span class="sxs-lookup"><span data-stu-id="3c333-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="3c333-110">`KeyDerivation.Pbkdf2`方法支持使用多个 PRFs （目前 `HMACSHA1` `HMACSHA256` 为、和 `HMACSHA512` ），而 `Rfc2898DeriveBytes` 类型仅支持 `HMACSHA1` 。</span><span class="sxs-lookup"><span data-stu-id="3c333-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="3c333-111">`KeyDerivation.Pbkdf2`方法将检测当前操作系统，并尝试选择最适合的例程实现，在某些情况下提供更好的性能。</span><span class="sxs-lookup"><span data-stu-id="3c333-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="3c333-112">（在 Windows 8 上，它提供的吞吐量大约为10倍 `Rfc2898DeriveBytes` 。）</span><span class="sxs-lookup"><span data-stu-id="3c333-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="3c333-113">`KeyDerivation.Pbkdf2`方法要求调用方指定所有参数（salt、PRF 和迭代计数）。</span><span class="sxs-lookup"><span data-stu-id="3c333-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="3c333-114">`Rfc2898DeriveBytes`类型为这些提供默认值。</span><span class="sxs-lookup"><span data-stu-id="3c333-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="3c333-115">有关实际用例，请参阅 ASP.NET Core 的类型的[源代码](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) Identity `PasswordHasher` 。</span><span class="sxs-lookup"><span data-stu-id="3c333-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
