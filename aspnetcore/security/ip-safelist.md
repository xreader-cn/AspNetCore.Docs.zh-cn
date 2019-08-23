---
title: ASP.NET Core 的客户端 IP 安全安全
author: damienbod
description: 了解如何编写中间件或操作筛选器, 以根据已批准的 IP 地址列表来验证远程 IP 地址。
ms.author: riande
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: 02e44135ab1742d44691cfda8c4167f21d6efa4e
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975651"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="7b982-103">ASP.NET Core 的客户端 IP 安全安全</span><span class="sxs-lookup"><span data-stu-id="7b982-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="7b982-104">作者: [Damien Bowden](https://twitter.com/damien_bod)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7b982-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="7b982-105">本文介绍三种在 ASP.NET Core 应用程序中实现 IP 安全列表 (也称为白名单) 的方法。</span><span class="sxs-lookup"><span data-stu-id="7b982-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="7b982-106">你可以使用:</span><span class="sxs-lookup"><span data-stu-id="7b982-106">You can use:</span></span>

* <span data-ttu-id="7b982-107">用于检查每个请求的远程 IP 地址的中间件。</span><span class="sxs-lookup"><span data-stu-id="7b982-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="7b982-108">操作筛选器来检查针对特定控制器或操作方法的请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="7b982-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="7b982-109">Razor Pages 筛选器来检查 Razor 页面的请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="7b982-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="7b982-110">在每种情况下, 包含批准的客户端 IP 地址的字符串存储在应用设置中。</span><span class="sxs-lookup"><span data-stu-id="7b982-110">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="7b982-111">中间件或筛选器会将字符串分析为一个列表, 并检查远程 IP 是否在列表中。</span><span class="sxs-lookup"><span data-stu-id="7b982-111">The middleware or filter parses the string into a list and checks if the remote IP is in the list.</span></span> <span data-ttu-id="7b982-112">如果不是, 则返回 HTTP 403 禁止的状态代码。</span><span class="sxs-lookup"><span data-stu-id="7b982-112">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="7b982-113">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7b982-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="7b982-114">安全安全</span><span class="sxs-lookup"><span data-stu-id="7b982-114">The safelist</span></span>

<span data-ttu-id="7b982-115">在*appsettings*文件中配置该列表。</span><span class="sxs-lookup"><span data-stu-id="7b982-115">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="7b982-116">它是以分号分隔的列表, 并且可以包含 IPv4 和 IPv6 地址。</span><span class="sxs-lookup"><span data-stu-id="7b982-116">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="7b982-117">中间件</span><span class="sxs-lookup"><span data-stu-id="7b982-117">Middleware</span></span>

<span data-ttu-id="7b982-118">`Configure`方法添加中间件, 并在构造函数参数中将安全项字符串传递给它。</span><span class="sxs-lookup"><span data-stu-id="7b982-118">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="7b982-119">中间件将字符串分析为数组, 并在数组中查找远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="7b982-119">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="7b982-120">如果找不到远程 IP 地址, 中间件将返回 HTTP 401 禁止访问。</span><span class="sxs-lookup"><span data-stu-id="7b982-120">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="7b982-121">对于 HTTP Get 请求, 将跳过此验证过程。</span><span class="sxs-lookup"><span data-stu-id="7b982-121">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="7b982-122">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="7b982-122">Action filter</span></span>

<span data-ttu-id="7b982-123">如果只希望特定控制器或操作方法使用 "安全", 请使用操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="7b982-123">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="7b982-124">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="7b982-124">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="7b982-125">操作筛选器将添加到服务容器中。</span><span class="sxs-lookup"><span data-stu-id="7b982-125">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="7b982-126">然后, 可以在控制器或操作方法上使用该筛选器。</span><span class="sxs-lookup"><span data-stu-id="7b982-126">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="7b982-127">在示例应用中, 筛选器应用`Get`于方法。</span><span class="sxs-lookup"><span data-stu-id="7b982-127">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="7b982-128">因此, 当你通过发送`Get` API 请求来测试应用时, 属性将验证客户端 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="7b982-128">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="7b982-129">当你通过使用任何其他 HTTP 方法调用 API 进行测试时, 中间件将验证客户端 IP。</span><span class="sxs-lookup"><span data-stu-id="7b982-129">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="7b982-130">Razor Pages 筛选器</span><span class="sxs-lookup"><span data-stu-id="7b982-130">Razor Pages filter</span></span> 

<span data-ttu-id="7b982-131">如果需要 Razor Pages 应用的安全安全, 请使用 Razor Pages 筛选器。</span><span class="sxs-lookup"><span data-stu-id="7b982-131">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="7b982-132">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="7b982-132">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="7b982-133">此筛选器通过将其添加到 MVC 筛选器集合来启用。</span><span class="sxs-lookup"><span data-stu-id="7b982-133">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="7b982-134">当你运行应用并请求 Razor 页面时, Razor Pages 筛选器将验证客户端 IP。</span><span class="sxs-lookup"><span data-stu-id="7b982-134">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7b982-135">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7b982-135">Next steps</span></span>

<span data-ttu-id="7b982-136">[了解有关 ASP.NET Core 中间件的详细信息](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="7b982-136">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
