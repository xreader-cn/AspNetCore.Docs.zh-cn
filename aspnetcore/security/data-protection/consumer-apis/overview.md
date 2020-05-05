---
title: ASP.NET Core 的使用者 Api 概述
author: rick-anderson
description: 获取 ASP.NET Core 数据保护库中可用的各种使用者 Api 的简要概述。
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767852"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a><span data-ttu-id="88e8f-103">ASP.NET Core 的使用者 Api 概述</span><span class="sxs-lookup"><span data-stu-id="88e8f-103">Consumer APIs overview for ASP.NET Core</span></span>

<span data-ttu-id="88e8f-104">`IDataProtectionProvider`和`IDataProtector`接口是使用者使用数据保护系统的基本接口。</span><span class="sxs-lookup"><span data-stu-id="88e8f-104">The `IDataProtectionProvider` and `IDataProtector` interfaces are the basic interfaces through which consumers use the data protection system.</span></span> <span data-ttu-id="88e8f-105">它们位于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/)文件包中。</span><span class="sxs-lookup"><span data-stu-id="88e8f-105">They're located in the [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) package.</span></span>

## <a name="idataprotectionprovider"></a><span data-ttu-id="88e8f-106">IDataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="88e8f-106">IDataProtectionProvider</span></span>

<span data-ttu-id="88e8f-107">提供程序接口表示数据保护系统的根。</span><span class="sxs-lookup"><span data-stu-id="88e8f-107">The provider interface represents the root of the data protection system.</span></span> <span data-ttu-id="88e8f-108">它不能直接用于保护或取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="88e8f-108">It cannot directly be used to protect or unprotect data.</span></span> <span data-ttu-id="88e8f-109">相反，使用者必须通过调用`IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)`来获取对的引用，其中目的是描述预期使用者用例的字符串。</span><span class="sxs-lookup"><span data-stu-id="88e8f-109">Instead, the consumer must get a reference to an `IDataProtector` by calling `IDataProtectionProvider.CreateProtector(purpose)`, where purpose is a string that describes the intended consumer use case.</span></span> <span data-ttu-id="88e8f-110">有关此参数意向的详细信息以及如何选择适当的值，请参阅[目的字符串](xref:security/data-protection/consumer-apis/purpose-strings)。</span><span class="sxs-lookup"><span data-stu-id="88e8f-110">See [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings) for much more information on the intent of this parameter and how to choose an appropriate value.</span></span>

## <a name="idataprotector"></a><span data-ttu-id="88e8f-111">IDataProtector</span><span class="sxs-lookup"><span data-stu-id="88e8f-111">IDataProtector</span></span>

<span data-ttu-id="88e8f-112">通过调用来`CreateProtector`返回保护程序接口，该接口是使用者可用于执行保护和取消保护操作的接口。</span><span class="sxs-lookup"><span data-stu-id="88e8f-112">The protector interface is returned by a call to `CreateProtector`, and it's this interface which consumers can use to perform protect and unprotect operations.</span></span>

<span data-ttu-id="88e8f-113">若要保护数据片段，请将数据传递给`Protect`方法。</span><span class="sxs-lookup"><span data-stu-id="88e8f-113">To protect a piece of data, pass the data to the `Protect` method.</span></span> <span data-ttu-id="88e8f-114">基本接口定义一个方法，该方法可将 byte [] 转换为 byte [] > byte []，但还有一个重载（提供作为扩展方法）来转换字符串 > 字符串。</span><span class="sxs-lookup"><span data-stu-id="88e8f-114">The basic interface defines a method which converts byte[] -> byte[], but there's also an overload (provided as an extension method) which converts string -> string.</span></span> <span data-ttu-id="88e8f-115">这两种方法提供的安全性完全相同;开发人员应选择最适合其用例的任何重载。</span><span class="sxs-lookup"><span data-stu-id="88e8f-115">The security offered by the two methods is identical; the developer should choose whichever overload is most convenient for their use case.</span></span> <span data-ttu-id="88e8f-116">无论选择哪种重载，protected 方法返回的值现在都受保护（到加密和篡改防），应用程序可以将其发送到不受信任的客户端。</span><span class="sxs-lookup"><span data-stu-id="88e8f-116">Irrespective of the overload chosen, the value returned by the Protect method is now protected (enciphered and tamper-proofed), and the application can send it to an untrusted client.</span></span>

<span data-ttu-id="88e8f-117">若要取消对以前受保护的数据片段的保护，请将受`Unprotect`保护的数据传递给方法。</span><span class="sxs-lookup"><span data-stu-id="88e8f-117">To unprotect a previously-protected piece of data, pass the protected data to the `Unprotect` method.</span></span> <span data-ttu-id="88e8f-118">（出于开发人员的方便，有基于字节 [] 的和基于字符串的重载。）如果在对此相同`Protect` `IDataProtector`的之前调用生成受保护的负载，则该`Unprotect`方法将返回未受保护的原始有效负载。</span><span class="sxs-lookup"><span data-stu-id="88e8f-118">(There are byte[]-based and string-based overloads for developer convenience.) If the protected payload was generated by an earlier call to `Protect` on this same `IDataProtector`, the `Unprotect` method will return the original unprotected payload.</span></span> <span data-ttu-id="88e8f-119">如果受保护的负载已被篡改或由不同`IDataProtector`的生成，则该`Unprotect`方法将引发 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="88e8f-119">If the protected payload has been tampered with or was produced by a different `IDataProtector`, the `Unprotect` method will throw CryptographicException.</span></span>

<span data-ttu-id="88e8f-120">相同和不同`IDataProtector`的概念将返回到目的概念。</span><span class="sxs-lookup"><span data-stu-id="88e8f-120">The concept of same vs. different `IDataProtector` ties back to the concept of purpose.</span></span> <span data-ttu-id="88e8f-121">如果两`IDataProtector`个实例是从同一根`IDataProtectionProvider`生成的`IDataProtectionProvider.CreateProtector`，而是通过对的调用中的不同目的字符串生成的，则它们被视为不同的[保护](xref:security/data-protection/consumer-apis/purpose-strings)程序，而一个将无法取消保护由另一个生成的负载。</span><span class="sxs-lookup"><span data-stu-id="88e8f-121">If two `IDataProtector` instances were generated from the same root `IDataProtectionProvider` but via different purpose strings in the call to `IDataProtectionProvider.CreateProtector`, then they're considered [different protectors](xref:security/data-protection/consumer-apis/purpose-strings), and one won't be able to unprotect payloads generated by the other.</span></span>

## <a name="consuming-these-interfaces"></a><span data-ttu-id="88e8f-122">使用这些接口</span><span class="sxs-lookup"><span data-stu-id="88e8f-122">Consuming these interfaces</span></span>

<span data-ttu-id="88e8f-123">对于支持 DI 的组件，预期的用法是组件在其构造函数中`IDataProtectionProvider`采用参数，而 DI 系统在实例化组件时自动提供此服务。</span><span class="sxs-lookup"><span data-stu-id="88e8f-123">For a DI-aware component, the intended usage is that the component takes an `IDataProtectionProvider` parameter in its constructor and that the DI system automatically provides this service when the component is instantiated.</span></span>

> [!NOTE]
> <span data-ttu-id="88e8f-124">某些应用程序（如控制台应用程序或 ASP.NET 4.x 应用程序）可能不能识别 DI，因此不能使用此处所述的机制。</span><span class="sxs-lookup"><span data-stu-id="88e8f-124">Some applications (such as console applications or ASP.NET 4.x applications) might not be DI-aware so cannot use the mechanism described here.</span></span> <span data-ttu-id="88e8f-125">对于这些方案，请参阅[非 DI 感知方案](xref:security/data-protection/configuration/non-di-scenarios)文档，详细了解如何获取`IDataProtection`提供程序的实例，而无需通过 DI。</span><span class="sxs-lookup"><span data-stu-id="88e8f-125">For these scenarios consult the [Non DI Aware Scenarios](xref:security/data-protection/configuration/non-di-scenarios) document for more information on getting an instance of an `IDataProtection` provider without going through DI.</span></span>

<span data-ttu-id="88e8f-126">下面的示例演示三个概念：</span><span class="sxs-lookup"><span data-stu-id="88e8f-126">The following sample demonstrates three concepts:</span></span>

1. <span data-ttu-id="88e8f-127">[将数据保护系统添加](xref:security/data-protection/configuration/overview)到服务容器中，</span><span class="sxs-lookup"><span data-stu-id="88e8f-127">[Add the data protection system](xref:security/data-protection/configuration/overview) to the service container,</span></span>

2. <span data-ttu-id="88e8f-128">使用 DI 接收的实例`IDataProtectionProvider`，然后</span><span class="sxs-lookup"><span data-stu-id="88e8f-128">Using DI to receive an instance of an `IDataProtectionProvider`, and</span></span>

3. <span data-ttu-id="88e8f-129">`IDataProtector`从创建`IDataProtectionProvider` ，并使用它来保护和取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="88e8f-129">Creating an `IDataProtector` from an `IDataProtectionProvider` and using it to protect and unprotect data.</span></span>

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="88e8f-130">包 AspNetCore 包含一个扩展方法`IServiceProvider.GetDataProtector` ，作为开发人员的便利。</span><span class="sxs-lookup"><span data-stu-id="88e8f-130">The package Microsoft.AspNetCore.DataProtection.Abstractions contains an extension method `IServiceProvider.GetDataProtector` as a developer convenience.</span></span> <span data-ttu-id="88e8f-131">它封装为一个操作， `IDataProtectionProvider`从服务提供程序中检索并调用。 `IDataProtectionProvider.CreateProtector`</span><span class="sxs-lookup"><span data-stu-id="88e8f-131">It encapsulates as a single operation both retrieving an `IDataProtectionProvider` from the service provider and calling `IDataProtectionProvider.CreateProtector`.</span></span> <span data-ttu-id="88e8f-132">下面的示例演示了其用法。</span><span class="sxs-lookup"><span data-stu-id="88e8f-132">The following sample demonstrates its usage.</span></span>

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> <span data-ttu-id="88e8f-133">`IDataProtectionProvider`和`IDataProtector`的实例对于多个调用方是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="88e8f-133">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="88e8f-134">它的目的是，在组件通过调用获取对的`IDataProtector`引用后`CreateProtector`，它会将该引用用于多次调用`Protect`和。 `Unprotect`</span><span class="sxs-lookup"><span data-stu-id="88e8f-134">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span> <span data-ttu-id="88e8f-135">如果无法验证`Unprotect`或解密受保护的有效负载，则对的调用将引发 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="88e8f-135">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="88e8f-136">某些组件可能希望在取消保护操作期间忽略错误;读取身份验证 cookie 的组件可能会处理此错误，并将请求视为根本没有 cookie，而不是完全失败的请求。</span><span class="sxs-lookup"><span data-stu-id="88e8f-136">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="88e8f-137">需要此行为的组件应专门捕获 System.security.cryptography.cryptographicexception，而不是抑制所有异常。</span><span class="sxs-lookup"><span data-stu-id="88e8f-137">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
