---
title: ASP.NET核心客户端 IP 安全列表
author: damienbod
description: 了解如何编写中间件或操作筛选器，以便根据已批准的 IP 地址列表验证远程 IP 地址。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471794"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="d4e53-103">ASP.NET核心客户端 IP 安全列表</span><span class="sxs-lookup"><span data-stu-id="d4e53-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="d4e53-104">由[达米安·鲍登](https://twitter.com/damien_bod)和[汤姆·戴克斯特拉](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d4e53-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="d4e53-105">本文介绍了在ASP.NET核心应用中实现 IP 地址安全列表（也称为允许列表）的三种方法。</span><span class="sxs-lookup"><span data-stu-id="d4e53-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="d4e53-106">随附的示例应用演示了所有三种方法。</span><span class="sxs-lookup"><span data-stu-id="d4e53-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="d4e53-107">可用工具如下：</span><span class="sxs-lookup"><span data-stu-id="d4e53-107">You can use:</span></span>

* <span data-ttu-id="d4e53-108">用于检查每个请求的远程 IP 地址的中间件。</span><span class="sxs-lookup"><span data-stu-id="d4e53-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="d4e53-109">MVC 操作筛选器，用于检查特定控制器或操作方法的请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="d4e53-110">Razor 页面筛选器以检查 Razor 页面请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="d4e53-111">在每种情况下，包含已批准的客户端 IP 地址的字符串都存储在应用设置中。</span><span class="sxs-lookup"><span data-stu-id="d4e53-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="d4e53-112">中间件或过滤器：</span><span class="sxs-lookup"><span data-stu-id="d4e53-112">The middleware or filter:</span></span>

* <span data-ttu-id="d4e53-113">将字符串解析为数组。</span><span class="sxs-lookup"><span data-stu-id="d4e53-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="d4e53-114">检查阵列中是否存在远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="d4e53-115">如果数组包含 IP 地址，则允许访问。</span><span class="sxs-lookup"><span data-stu-id="d4e53-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="d4e53-116">否则，将返回 HTTP 403 禁止状态代码。</span><span class="sxs-lookup"><span data-stu-id="d4e53-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="d4e53-117">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d4e53-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="d4e53-118">IP 地址安全列表</span><span class="sxs-lookup"><span data-stu-id="d4e53-118">IP address safelist</span></span>

<span data-ttu-id="d4e53-119">在示例应用中，IP 地址安全列表为：</span><span class="sxs-lookup"><span data-stu-id="d4e53-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="d4e53-120">由`AdminSafeList`*appsettings.json*文件中的属性定义。</span><span class="sxs-lookup"><span data-stu-id="d4e53-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="d4e53-121">一个分号分隔字符串，可能包含 Internet[协议版本 4 （IPv4）](https://wikipedia.org/wiki/IPv4)和[Internet 协议版本 6 （IPv6）](https://wikipedia.org/wiki/IPv6)地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="d4e53-122">在前面的示例中，`127.0.0.1`允许 和`192.168.1.5`的 IPv4 地址和`::1`IPv6 回回地址（压缩格式`0:0:0:0:0:0:0:1`）。</span><span class="sxs-lookup"><span data-stu-id="d4e53-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="d4e53-123">中间件</span><span class="sxs-lookup"><span data-stu-id="d4e53-123">Middleware</span></span>

<span data-ttu-id="d4e53-124">该方法`Startup.Configure`将自定义`AdminSafeListMiddleware`中间件类型添加到应用的请求管道中。</span><span class="sxs-lookup"><span data-stu-id="d4e53-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="d4e53-125">使用 .NET Core 配置提供程序检索安全列表，并作为构造函数参数传递。</span><span class="sxs-lookup"><span data-stu-id="d4e53-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="d4e53-126">中间件将字符串解析为数组并搜索数组中的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="d4e53-127">如果未找到远程 IP 地址，中间件将返回 HTTP 403 禁止。</span><span class="sxs-lookup"><span data-stu-id="d4e53-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="d4e53-128">此验证过程是绕过 HTTP GET 请求的。</span><span class="sxs-lookup"><span data-stu-id="d4e53-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="d4e53-129">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="d4e53-129">Action filter</span></span>

<span data-ttu-id="d4e53-130">如果需要针对特定 MVC 控制器或操作方法的安全列表驱动访问控制，请使用操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="d4e53-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="d4e53-131">例如：</span><span class="sxs-lookup"><span data-stu-id="d4e53-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="d4e53-132">在`Startup.ConfigureServices`中，将操作筛选器添加到 MVC 筛选器集合。</span><span class="sxs-lookup"><span data-stu-id="d4e53-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="d4e53-133">在下面的示例中，将添加`ClientIpCheckActionFilter`操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="d4e53-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="d4e53-134">安全列表和控制台记录器实例作为构造函数参数传递。</span><span class="sxs-lookup"><span data-stu-id="d4e53-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="d4e53-135">然后，操作筛选器可以应用于具有[[服务筛选器]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute)属性的控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="d4e53-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="d4e53-136">在示例应用中，操作筛选器应用于控制器的操作`Get`方法。</span><span class="sxs-lookup"><span data-stu-id="d4e53-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="d4e53-137">通过发送测试应用时：</span><span class="sxs-lookup"><span data-stu-id="d4e53-137">When you test the app by sending:</span></span>

* <span data-ttu-id="d4e53-138">HTTP GET 请求，`[ServiceFilter]`属性验证客户端 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="d4e53-139">如果允许访问`Get`操作方法，操作筛选器和操作方法将生成以下控制台输出的变体：</span><span class="sxs-lookup"><span data-stu-id="d4e53-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="d4e53-140">除了 GET 之外，`AdminSafeListMiddleware`中间件的 HTTP 请求谓词将验证客户端 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="razor-pages-filter"></a><span data-ttu-id="d4e53-141">剃刀页面过滤器</span><span class="sxs-lookup"><span data-stu-id="d4e53-141">Razor Pages filter</span></span>

<span data-ttu-id="d4e53-142">如果需要 Razor Pages 应用的安全列表驱动访问控制，请使用 Razor 页面筛选器。</span><span class="sxs-lookup"><span data-stu-id="d4e53-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="d4e53-143">例如：</span><span class="sxs-lookup"><span data-stu-id="d4e53-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="d4e53-144">在`Startup.ConfigureServices`中，通过将 Razor 页面添加到 MVC 筛选器集合，使其启用该筛选器。</span><span class="sxs-lookup"><span data-stu-id="d4e53-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="d4e53-145">在下面的示例中，将添加`ClientIpCheckPageFilter`Razor 页面筛选器。</span><span class="sxs-lookup"><span data-stu-id="d4e53-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="d4e53-146">安全列表和控制台记录器实例作为构造函数参数传递。</span><span class="sxs-lookup"><span data-stu-id="d4e53-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="d4e53-147">当请求示例应用的*索引*Razor 页面时，"Razor 页面"筛选器将验证客户端 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="d4e53-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="d4e53-148">筛选器生成以下控制台输出的变体：</span><span class="sxs-lookup"><span data-stu-id="d4e53-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="d4e53-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="d4e53-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="d4e53-150">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="d4e53-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
