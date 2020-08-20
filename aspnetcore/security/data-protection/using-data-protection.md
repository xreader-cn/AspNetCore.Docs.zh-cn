---
title: ASP.NET Core 中的数据保护 Api 入门
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 在应用中保护和取消保护数据。
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: bfe1dc800f65eaca00bb1dd145d6ecc4159b783f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631675"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="dab56-103">ASP.NET Core 中的数据保护 Api 入门</span><span class="sxs-lookup"><span data-stu-id="dab56-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="dab56-104">最简单的是保护数据，包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="dab56-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="dab56-105">从数据保护提供程序创建数据保护程序。</span><span class="sxs-lookup"><span data-stu-id="dab56-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="dab56-106">`Protect`用要保护的数据调用方法。</span><span class="sxs-lookup"><span data-stu-id="dab56-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="dab56-107">`Unprotect`用要恢复为纯文本的数据调用方法。</span><span class="sxs-lookup"><span data-stu-id="dab56-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="dab56-108">大多数框架和应用模型（如 ASP.NET Core 或 SignalR ）已配置数据保护系统，并将其添加到通过依赖关系注入访问的服务容器。</span><span class="sxs-lookup"><span data-stu-id="dab56-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="dab56-109">下面的示例演示如何为依赖关系注入配置服务容器并注册数据保护堆栈，通过 DI 接收数据保护提供程序，创建保护程序并保护然后取消保护数据。</span><span class="sxs-lookup"><span data-stu-id="dab56-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="dab56-110">创建保护程序时，必须提供一个或多个 [目的字符串](xref:security/data-protection/consumer-apis/purpose-strings)。</span><span class="sxs-lookup"><span data-stu-id="dab56-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="dab56-111">用途字符串提供使用者之间的隔离。</span><span class="sxs-lookup"><span data-stu-id="dab56-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="dab56-112">例如，使用 "绿色" 目的字符串创建的保护程序将无法取消保护由 "紫色" 目的的保护程序提供的数据。</span><span class="sxs-lookup"><span data-stu-id="dab56-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="dab56-113">和的 `IDataProtectionProvider` 实例 `IDataProtector` 对于多个调用方是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="dab56-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="dab56-114">它的目的是，在组件通过调用获取对的引用后 `IDataProtector` `CreateProtector` ，它会将该引用用于多次调用 `Protect` 和 `Unprotect` 。</span><span class="sxs-lookup"><span data-stu-id="dab56-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="dab56-115">`Unprotect`如果无法验证或解密受保护的有效负载，则对的调用将引发 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="dab56-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="dab56-116">某些组件可能希望在取消保护操作期间忽略错误;读取身份验证的组件 cookie 可能会处理此错误，并将该请求视为 cookie 根本不会失败，而不是完全导致请求失败。</span><span class="sxs-lookup"><span data-stu-id="dab56-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="dab56-117">需要此行为的组件应专门捕获 System.security.cryptography.cryptographicexception，而不是抑制所有异常。</span><span class="sxs-lookup"><span data-stu-id="dab56-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
