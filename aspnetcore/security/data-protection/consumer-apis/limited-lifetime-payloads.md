---
title: 在 ASP.NET Core 中限制受保护负载的生存期
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 限制受保护负载的生存期。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060789"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="abc0b-103">在 ASP.NET Core 中限制受保护负载的生存期</span><span class="sxs-lookup"><span data-stu-id="abc0b-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="abc0b-104">在某些情况下，应用程序开发人员需要创建在设定的时间段之后过期的受保护负载。</span><span class="sxs-lookup"><span data-stu-id="abc0b-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="abc0b-105">例如，受保护的负载可能表示密码重置令牌，该令牌仅应在一小时内有效。</span><span class="sxs-lookup"><span data-stu-id="abc0b-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="abc0b-106">当然，开发人员也可以创建自己的负载格式，其中包含一个嵌入的过期日期，而高级开发人员可能希望这样做，但对于大多数管理这些过期的开发人员来说，这种情况都很繁琐。</span><span class="sxs-lookup"><span data-stu-id="abc0b-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="abc0b-107">为了使我们的开发人员受众更容易，包 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 包含用于创建在设定的时间段后自动过期的有效负载的实用工具 api。</span><span class="sxs-lookup"><span data-stu-id="abc0b-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="abc0b-108">这些 Api 挂起 `ITimeLimitedDataProtector` 类型。</span><span class="sxs-lookup"><span data-stu-id="abc0b-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="abc0b-109">API 使用情况</span><span class="sxs-lookup"><span data-stu-id="abc0b-109">API usage</span></span>

<span data-ttu-id="abc0b-110">`ITimeLimitedDataProtector`接口是用于保护和取消保护时间限制/自过期负载的核心接口。</span><span class="sxs-lookup"><span data-stu-id="abc0b-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="abc0b-111">若要创建的实例 `ITimeLimitedDataProtector` ，首先需要使用特定用途构造的常规 [IDataProtector](xref:security/data-protection/consumer-apis/overview) 的实例。</span><span class="sxs-lookup"><span data-stu-id="abc0b-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="abc0b-112">`IDataProtector`实例可用后，调用 `IDataProtector.ToTimeLimitedDataProtector` 扩展方法以获取带有内置过期功能的保护程序。</span><span class="sxs-lookup"><span data-stu-id="abc0b-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="abc0b-113">`ITimeLimitedDataProtector` 公开以下 API surface 和 extension 方法：</span><span class="sxs-lookup"><span data-stu-id="abc0b-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="abc0b-114">CreateProtector (string 目的) ： ITimeLimitedDataProtector-此 API 类似于现有的 `IDataProtectionProvider.CreateProtector` ，因为它可用于从根时间限制的保护程序创建 [用途链](xref:security/data-protection/consumer-apis/purpose-strings) 。</span><span class="sxs-lookup"><span data-stu-id="abc0b-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="abc0b-115">保护 (byte [] 纯文本、DateTimeOffset 过期) ： byte []</span><span class="sxs-lookup"><span data-stu-id="abc0b-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="abc0b-116">保护 (byte [] 纯文本、TimeSpan 生存期) ： byte []</span><span class="sxs-lookup"><span data-stu-id="abc0b-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="abc0b-117">保护 (byte [] 纯文本) ： byte []</span><span class="sxs-lookup"><span data-stu-id="abc0b-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="abc0b-118">保护 (字符串纯文本、DateTimeOffset 过期) ： string</span><span class="sxs-lookup"><span data-stu-id="abc0b-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="abc0b-119">保护 (字符串纯文本、TimeSpan 生存期) ： string</span><span class="sxs-lookup"><span data-stu-id="abc0b-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="abc0b-120">保护 (字符串纯文本) ： string</span><span class="sxs-lookup"><span data-stu-id="abc0b-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="abc0b-121">除了使用 `Protect` 纯文本的核心方法之外，还有一些新的重载，可用于指定有效负载的到期日期。</span><span class="sxs-lookup"><span data-stu-id="abc0b-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="abc0b-122">可以通过) 将到期日期指定为通过 `DateTimeOffset`) 或从当前系统时间 (的相对时间 (的绝对日期 `TimeSpan` 。</span><span class="sxs-lookup"><span data-stu-id="abc0b-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="abc0b-123">如果调用不带过期的重载，则假定负载永不过期。</span><span class="sxs-lookup"><span data-stu-id="abc0b-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="abc0b-124">取消保护 (byte [] protectedData，out DateTimeOffset 过期) ： byte []</span><span class="sxs-lookup"><span data-stu-id="abc0b-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="abc0b-125">取消保护 (byte [] protectedData) ： byte []</span><span class="sxs-lookup"><span data-stu-id="abc0b-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="abc0b-126">取消保护 (字符串 protectedData，out DateTimeOffset 过期) ： string</span><span class="sxs-lookup"><span data-stu-id="abc0b-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="abc0b-127">取消保护 (字符串 protectedData) ： string</span><span class="sxs-lookup"><span data-stu-id="abc0b-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="abc0b-128">`Unprotect`方法返回未受保护的原始数据。</span><span class="sxs-lookup"><span data-stu-id="abc0b-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="abc0b-129">如果负载尚未过期，则将以可选的 out 参数形式返回绝对过期，并将其作为不受保护的原始数据。</span><span class="sxs-lookup"><span data-stu-id="abc0b-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="abc0b-130">如果有效负载已过期，则解除保护方法的所有重载都将引发 System.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="abc0b-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="abc0b-131">不建议使用这些 Api 来保护需要长期或无限持久性的负载。</span><span class="sxs-lookup"><span data-stu-id="abc0b-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="abc0b-132">"我可以承受受保护的负载在一个月后永久无法恢复吗？"</span><span class="sxs-lookup"><span data-stu-id="abc0b-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="abc0b-133">可以作为最佳经验法则;如果答案不是，开发人员应考虑其他 Api。</span><span class="sxs-lookup"><span data-stu-id="abc0b-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="abc0b-134">下面的示例使用 [非 DI 代码路径](xref:security/data-protection/configuration/non-di-scenarios) 来实例化数据保护系统。</span><span class="sxs-lookup"><span data-stu-id="abc0b-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="abc0b-135">若要运行此示例，请确保首先添加了对 AspNetCore 包的引用。</span><span class="sxs-lookup"><span data-stu-id="abc0b-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
