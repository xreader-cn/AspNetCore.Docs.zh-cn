---
title: 适用于 ASP.NET Core 的客户端 IP 安全列表
author: damienbod
description: 了解如何编写中间件或操作筛选器来验证针对一组已批准的 IP 地址的远程 IP 地址。
ms.author: tdykstra
ms.custom: mvc
ms.date: 08/31/2018
uid: security/ip-safelist
ms.openlocfilehash: cfbb50ea33ae3af577f13b00bccc75fe0be57f79
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898144"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="84175-103">适用于 ASP.NET Core 的客户端 IP 安全列表</span><span class="sxs-lookup"><span data-stu-id="84175-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="84175-104">通过[Damien Bowden](https://twitter.com/damien_bod)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="84175-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="84175-105">本文介绍三种方法可以在 ASP.NET Core 应用中实施 IP 安全列表 （也称为允许列表）。</span><span class="sxs-lookup"><span data-stu-id="84175-105">This article shows three ways to implement an IP safelist (also known as a whitelist) in an ASP.NET Core app.</span></span> <span data-ttu-id="84175-106">您可以使用：</span><span class="sxs-lookup"><span data-stu-id="84175-106">You can use:</span></span>

* <span data-ttu-id="84175-107">若要检查的每个请求的远程 IP 地址的中间件。</span><span class="sxs-lookup"><span data-stu-id="84175-107">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="84175-108">若要检查为特定控制器或操作方法的请求的远程 IP 地址的操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="84175-108">Action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="84175-109">Razor 页面筛选器，以检查 Razor 页的请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="84175-109">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="84175-110">示例应用程序说明了这两种方法。</span><span class="sxs-lookup"><span data-stu-id="84175-110">The sample app illustrates both approaches.</span></span> <span data-ttu-id="84175-111">在每种情况下，包含已批准的客户端 IP 地址的字符串存储在一个应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="84175-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="84175-112">中间件或筛选器将字符串分析为列表，并检查远程 IP 是否在列表中。</span><span class="sxs-lookup"><span data-stu-id="84175-112">The middleware or filter parses the string into a list and  checks if the remote IP is in the list.</span></span> <span data-ttu-id="84175-113">如果没有，则返回 HTTP 403 禁止访问状态代码。</span><span class="sxs-lookup"><span data-stu-id="84175-113">If not, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="84175-114">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="84175-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples/2.x/ClientIpAspNetCore) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-safelist"></a><span data-ttu-id="84175-115">安全列表</span><span class="sxs-lookup"><span data-stu-id="84175-115">The safelist</span></span>

<span data-ttu-id="84175-116">在配置列表*appsettings.json*文件。</span><span class="sxs-lookup"><span data-stu-id="84175-116">The list is configured in the *appsettings.json* file.</span></span> <span data-ttu-id="84175-117">它是以分号分隔的列表，并且可以包含 IPv4 和 IPv6 地址。</span><span class="sxs-lookup"><span data-stu-id="84175-117">It's a semicolon-delimited list and can contain IPv4 and IPv6 addresses.</span></span>

[!code-json[](ip-safelist/samples/2.x/ClientIpAspNetCore/appsettings.json?highlight=2)]

## <a name="middleware"></a><span data-ttu-id="84175-118">中间件</span><span class="sxs-lookup"><span data-stu-id="84175-118">Middleware</span></span>

<span data-ttu-id="84175-119">`Configure`方法添加中间件，并向其构造函数参数中传递的安全列表的字符串。</span><span class="sxs-lookup"><span data-stu-id="84175-119">The `Configure` method adds the middleware and passes the safelist string to it in a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_Configure&highlight=7)]

<span data-ttu-id="84175-120">中间件将字符串分析为一个数组，并查找数组中的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="84175-120">The middleware parses the string into an array and looks for the remote IP address in the array.</span></span> <span data-ttu-id="84175-121">如果找不到的远程 IP 地址，中间件返回 HTTP 401 禁止访问。</span><span class="sxs-lookup"><span data-stu-id="84175-121">If the remote IP address is not found, the middleware returns HTTP 401 Forbidden.</span></span> <span data-ttu-id="84175-122">对于 HTTP Get 请求跳过此验证过程。</span><span class="sxs-lookup"><span data-stu-id="84175-122">This validation process is bypassed for HTTP Get requests.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="84175-123">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="84175-123">Action filter</span></span>

<span data-ttu-id="84175-124">如果您希望仅为特定控制器或操作方法的安全列表，请使用操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="84175-124">If you want a safelist only for specific controllers or action methods, use an action filter.</span></span> <span data-ttu-id="84175-125">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="84175-125">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckFilter.cs)]

<span data-ttu-id="84175-126">操作筛选器添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="84175-126">The action filter is added to the services container.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="84175-127">然后可以在控制器或操作方法上使用筛选器。</span><span class="sxs-lookup"><span data-stu-id="84175-127">The filter can then be used on a controller or action method.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_Filter&highlight=1)]

<span data-ttu-id="84175-128">在示例应用中，筛选器应用于`Get`方法。</span><span class="sxs-lookup"><span data-stu-id="84175-128">In the sample app, the filter is applied to the `Get` method.</span></span> <span data-ttu-id="84175-129">因此，当您测试应用程序通过发送`Get`API 请求，该属性就验证客户端 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="84175-129">So when you test the app by sending a `Get` API request, the attribute is validating the client IP address.</span></span> <span data-ttu-id="84175-130">当测试通过使用任何其他 HTTP 方法调用的 API 时，中间件就验证客户端 IP。</span><span class="sxs-lookup"><span data-stu-id="84175-130">When you test by calling the API with any other HTTP method, the middleware is validating the client IP.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="84175-131">Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="84175-131">Razor Pages filter</span></span> 

<span data-ttu-id="84175-132">如果你想为 Razor 页面应用的安全列表，使用 Razor 页面筛选器。</span><span class="sxs-lookup"><span data-stu-id="84175-132">If you want a safelist for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="84175-133">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="84175-133">Here's an example:</span></span> 

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Filters/ClientIdCheckPageFilter.cs)]

<span data-ttu-id="84175-134">通过将其添加到 MVC 筛选器集合启用该筛选器。</span><span class="sxs-lookup"><span data-stu-id="84175-134">This filter is enabled by adding it to the MVC Filters collection.</span></span>

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

<span data-ttu-id="84175-135">当您运行该应用程序，并请求 Razor 页面时，Razor 页面筛选器就验证客户端 IP。</span><span class="sxs-lookup"><span data-stu-id="84175-135">When you run the app and request a Razor page, the Razor Pages filter is validating the client IP.</span></span>

## <a name="next-steps"></a><span data-ttu-id="84175-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="84175-136">Next steps</span></span>

<span data-ttu-id="84175-137">[了解有关 ASP.NET Core 中间件的详细信息](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="84175-137">[Learn more about ASP.NET Core Middleware](xref:fundamentals/middleware/index).</span></span>
