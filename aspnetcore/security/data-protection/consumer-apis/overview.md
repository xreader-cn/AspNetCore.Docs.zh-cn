---
title: ASP.NET Core 的使用者 Api 概述
author: rick-anderson
description: 获取 ASP.NET Core 数据保护库中可用的各种使用者 Api 的简要概述。
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 0cdc5d8700357f33fcd3d361f10a5d66cccb067d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629894"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a><span data-ttu-id="e1e28-103">ASP.NET Core 的使用者 Api 概述</span><span class="sxs-lookup"><span data-stu-id="e1e28-103">Consumer APIs overview for ASP.NET Core</span></span>

<span data-ttu-id="e1e28-104">`IDataProtectionProvider`和 `IDataProtector` 接口是使用者使用数据保护系统的基本接口。</span><span class="sxs-lookup"><span data-stu-id="e1e28-104">The `IDataProtectionProvider` and `IDataProtector` interfaces are the basic interfaces through which consumers use the data protection system.</span></span> <span data-ttu-id="e1e28-105">它们位于 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 文件包中。</span><span class="sxs-lookup"><span data-stu-id="e1e28-105">They're located in the [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) package.</span></span>

## <a name="idataprotectionprovider"></a><span data-ttu-id="e1e28-106">IDataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="e1e28-106">IDataProtectionProvider</span></span>

<span data-ttu-id="e1e28-107">提供程序接口表示数据保护系统的根。</span><span class="sxs-lookup"><span data-stu-id="e1e28-107">The provider interface represents the root of the data protection system.</span></span> <span data-ttu-id="e1e28-108">它不能直接用于保护或取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="e1e28-108">It cannot directly be used to protect or unprotect data.</span></span> <span data-ttu-id="e1e28-109">相反，使用者必须通过调用来获取对的引用 `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` ，其中目的是描述预期使用者用例的字符串。</span><span class="sxs-lookup"><span data-stu-id="e1e28-109">Instead, the consumer must get a reference to an `IDataProtector` by calling `IDataProtectionProvider.CreateProtector(purpose)`, where purpose is a string that describes the intended consumer use case.</span></span> <span data-ttu-id="e1e28-110">有关此参数意向的详细信息以及如何选择适当的值，请参阅 [目的字符串](xref:security/data-protection/consumer-apis/purpose-strings) 。</span><span class="sxs-lookup"><span data-stu-id="e1e28-110">See [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings) for much more information on the intent of this parameter and how to choose an appropriate value.</span></span>

## <a name="idataprotector"></a><span data-ttu-id="e1e28-111">IDataProtector</span><span class="sxs-lookup"><span data-stu-id="e1e28-111">IDataProtector</span></span>

<span data-ttu-id="e1e28-112">通过调用来返回保护程序接口，该接口是 `CreateProtector` 使用者可用于执行保护和取消保护操作的接口。</span><span class="sxs-lookup"><span data-stu-id="e1e28-112">The protector interface is returned by a call to `CreateProtector`, and it's this interface which consumers can use to perform protect and unprotect operations.</span></span>

<span data-ttu-id="e1e28-113">若要保护数据片段，请将数据传递给 `Protect` 方法。</span><span class="sxs-lookup"><span data-stu-id="e1e28-113">To protect a piece of data, pass the data to the `Protect` method.</span></span> <span data-ttu-id="e1e28-114">基本接口定义一个方法，该方法可将 byte [] 转换为 byte [] > byte []，但还有一个重载 (提供作为扩展方法的重载) 用于转换字符串 > 字符串。</span><span class="sxs-lookup"><span data-stu-id="e1e28-114">The basic interface defines a method which converts byte[] -> byte[], but there's also an overload (provided as an extension method) which converts string -> string.</span></span> <span data-ttu-id="e1e28-115">这两种方法提供的安全性完全相同;开发人员应选择最适合其用例的任何重载。</span><span class="sxs-lookup"><span data-stu-id="e1e28-115">The security offered by the two methods is identical; the developer should choose whichever overload is most convenient for their use case.</span></span> <span data-ttu-id="e1e28-116">无论选择哪种重载，protected 方法返回的值现在都受保护， (到加密和篡改防) ，应用程序可以将其发送到不受信任的客户端。</span><span class="sxs-lookup"><span data-stu-id="e1e28-116">Irrespective of the overload chosen, the value returned by the Protect method is now protected (enciphered and tamper-proofed), and the application can send it to an untrusted client.</span></span>

<span data-ttu-id="e1e28-117">若要取消对以前受保护的数据片段的保护，请将受保护的数据传递给 `Unprotect` 方法。</span><span class="sxs-lookup"><span data-stu-id="e1e28-117">To unprotect a previously-protected piece of data, pass the protected data to the `Unprotect` method.</span></span> <span data-ttu-id="e1e28-118"> (有基于字节 [] 的和基于字符串的重载，以便为开发人员提供便利。 ) 如果受保护的负载是通过对此相同的之前调用生成的 `Protect` `IDataProtector` ，则该 `Unprotect` 方法将返回未受保护的原始有效负载。</span><span class="sxs-lookup"><span data-stu-id="e1e28-118">(There are byte[]-based and string-based overloads for developer convenience.) If the protected payload was generated by an earlier call to `Protect` on this same `IDataProtector`, the `Unprotect` method will return the original unprotected payload.</span></span> <span data-ttu-id="e1e28-119">如果受保护的负载已被篡改或由不同的生成 `IDataProtector` ，则该 `Unprotect` 方法将引发 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="e1e28-119">If the protected payload has been tampered with or was produced by a different `IDataProtector`, the `Unprotect` method will throw CryptographicException.</span></span>

<span data-ttu-id="e1e28-120">相同和不同的概念将 `IDataProtector` 返回到目的概念。</span><span class="sxs-lookup"><span data-stu-id="e1e28-120">The concept of same vs. different `IDataProtector` ties back to the concept of purpose.</span></span> <span data-ttu-id="e1e28-121">如果两个 `IDataProtector` 实例是从同一根生成的 `IDataProtectionProvider` ，而是通过对的调用中的不同目的字符串生成的 `IDataProtectionProvider.CreateProtector` ，则它们被视为 [不同的保护](xref:security/data-protection/consumer-apis/purpose-strings)程序，而一个将无法取消保护由另一个生成的负载。</span><span class="sxs-lookup"><span data-stu-id="e1e28-121">If two `IDataProtector` instances were generated from the same root `IDataProtectionProvider` but via different purpose strings in the call to `IDataProtectionProvider.CreateProtector`, then they're considered [different protectors](xref:security/data-protection/consumer-apis/purpose-strings), and one won't be able to unprotect payloads generated by the other.</span></span>

## <a name="consuming-these-interfaces"></a><span data-ttu-id="e1e28-122">使用这些接口</span><span class="sxs-lookup"><span data-stu-id="e1e28-122">Consuming these interfaces</span></span>

<span data-ttu-id="e1e28-123">对于支持 DI 的组件，预期的用法是组件 `IDataProtectionProvider` 在其构造函数中采用参数，而 DI 系统在实例化组件时自动提供此服务。</span><span class="sxs-lookup"><span data-stu-id="e1e28-123">For a DI-aware component, the intended usage is that the component takes an `IDataProtectionProvider` parameter in its constructor and that the DI system automatically provides this service when the component is instantiated.</span></span>

> [!NOTE]
> <span data-ttu-id="e1e28-124">某些应用程序 (如控制台应用程序或 ASP.NET 4.x 应用程序) 可能不是 DI 可识别的，因此无法使用此处所述的机制。</span><span class="sxs-lookup"><span data-stu-id="e1e28-124">Some applications (such as console applications or ASP.NET 4.x applications) might not be DI-aware so cannot use the mechanism described here.</span></span> <span data-ttu-id="e1e28-125">对于这些方案，请参阅 [非 DI 感知方案](xref:security/data-protection/configuration/non-di-scenarios) 文档，详细了解如何获取提供程序的实例， `IDataProtection` 而无需通过 DI。</span><span class="sxs-lookup"><span data-stu-id="e1e28-125">For these scenarios consult the [Non DI Aware Scenarios](xref:security/data-protection/configuration/non-di-scenarios) document for more information on getting an instance of an `IDataProtection` provider without going through DI.</span></span>

<span data-ttu-id="e1e28-126">下面的示例演示三个概念：</span><span class="sxs-lookup"><span data-stu-id="e1e28-126">The following sample demonstrates three concepts:</span></span>

1. <span data-ttu-id="e1e28-127">[将数据保护系统添加](xref:security/data-protection/configuration/overview) 到服务容器中，</span><span class="sxs-lookup"><span data-stu-id="e1e28-127">[Add the data protection system](xref:security/data-protection/configuration/overview) to the service container,</span></span>

2. <span data-ttu-id="e1e28-128">使用 DI 接收的实例 `IDataProtectionProvider` ，然后</span><span class="sxs-lookup"><span data-stu-id="e1e28-128">Using DI to receive an instance of an `IDataProtectionProvider`, and</span></span>

3. <span data-ttu-id="e1e28-129">从创建 `IDataProtector` `IDataProtectionProvider` ，并使用它来保护和取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="e1e28-129">Creating an `IDataProtector` from an `IDataProtectionProvider` and using it to protect and unprotect data.</span></span>

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="e1e28-130">包 AspNetCore 包含一个扩展方法 `IServiceProvider.GetDataProtector` ，作为开发人员的便利。</span><span class="sxs-lookup"><span data-stu-id="e1e28-130">The package Microsoft.AspNetCore.DataProtection.Abstractions contains an extension method `IServiceProvider.GetDataProtector` as a developer convenience.</span></span> <span data-ttu-id="e1e28-131">它封装为一个操作， `IDataProtectionProvider` 从服务提供程序中检索并调用 `IDataProtectionProvider.CreateProtector` 。</span><span class="sxs-lookup"><span data-stu-id="e1e28-131">It encapsulates as a single operation both retrieving an `IDataProtectionProvider` from the service provider and calling `IDataProtectionProvider.CreateProtector`.</span></span> <span data-ttu-id="e1e28-132">下面的示例演示了其用法。</span><span class="sxs-lookup"><span data-stu-id="e1e28-132">The following sample demonstrates its usage.</span></span>

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> <span data-ttu-id="e1e28-133">和的 `IDataProtectionProvider` 实例 `IDataProtector` 对于多个调用方是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="e1e28-133">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="e1e28-134">它的目的是，在组件通过调用获取对的引用后 `IDataProtector` `CreateProtector` ，它会将该引用用于多次调用 `Protect` 和 `Unprotect` 。</span><span class="sxs-lookup"><span data-stu-id="e1e28-134">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span> <span data-ttu-id="e1e28-135">`Unprotect`如果无法验证或解密受保护的有效负载，则对的调用将引发 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="e1e28-135">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="e1e28-136">某些组件可能希望在取消保护操作期间忽略错误;读取身份验证的组件 cookie 可能会处理此错误，并将该请求视为 cookie 根本不会失败，而不是完全导致请求失败。</span><span class="sxs-lookup"><span data-stu-id="e1e28-136">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="e1e28-137">需要此行为的组件应专门捕获 System.security.cryptography.cryptographicexception，而不是抑制所有异常。</span><span class="sxs-lookup"><span data-stu-id="e1e28-137">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
