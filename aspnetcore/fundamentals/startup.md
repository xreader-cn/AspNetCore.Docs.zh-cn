---
title: ASP.NET Core 中的应用启动
author: rick-anderson
description: 了解 ASP.NET Core 中的 Startup 类如何配置服务和应用的请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/startup
ms.openlocfilehash: 747b13abb0ce3fed2d1dc018c6dbf82db1ae7130
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052235"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="c9455-103">ASP.NET Core 中的应用启动</span><span class="sxs-lookup"><span data-stu-id="c9455-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="c9455-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="c9455-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="c9455-105">`Startup` 类配置服务和应用的请求管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-105">The `Startup` class configures services and the app's request pipeline.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="c9455-106">Startup 类</span><span class="sxs-lookup"><span data-stu-id="c9455-106">The Startup class</span></span>

<span data-ttu-id="c9455-107">ASP.NET Core 应用使用 `Startup` 类，按照约定命名为 `Startup`。</span><span class="sxs-lookup"><span data-stu-id="c9455-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="c9455-108">`Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="c9455-108">The `Startup` class:</span></span>

* <span data-ttu-id="c9455-109">可选择性地包括 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 方法以配置应用的服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="c9455-110">服务是一个提供应用功能的可重用组件。</span><span class="sxs-lookup"><span data-stu-id="c9455-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="c9455-111">在 `ConfigureServices` 中注册服务，并通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 或 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> 在整个应用中使用服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="c9455-112">包括 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 方法以创建应用的请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="c9455-113">在应用启动时，ASP.NET Core 运行时会调用 `ConfigureServices` 和 `Configure`：</span><span class="sxs-lookup"><span data-stu-id="c9455-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="c9455-114">前面的示例适用于 [:::no-loc(Razor)::: Pages](xref:razor-pages/index)；MVC 版本类似。</span><span class="sxs-lookup"><span data-stu-id="c9455-114">The preceding sample is for [:::no-loc(Razor)::: Pages](xref:razor-pages/index); the MVC version is similar.</span></span>


<span data-ttu-id="c9455-115">在构建应用[主机](xref:fundamentals/index#host)时指定 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="c9455-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="c9455-116">通常，通过在主机生成器上调用 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 方法来指定 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="c9455-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="c9455-117">主机提供 `Startup` 类构造函数可用的某些服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="c9455-118">应用通过 `ConfigureServices` 添加其他服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="c9455-119">主机和应用服务都可以在 `Configure` 和整个应用中使用。</span><span class="sxs-lookup"><span data-stu-id="c9455-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="c9455-120">使用[泛型主机](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) 时，只能将以下服务类型注入 `Startup` 构造函数：</span><span class="sxs-lookup"><span data-stu-id="c9455-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="c9455-121">在调用 `Configure` 方法之前，大多数服务都不可用。</span><span class="sxs-lookup"><span data-stu-id="c9455-121">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="c9455-122">多启动</span><span class="sxs-lookup"><span data-stu-id="c9455-122">Multiple Startup</span></span>

<span data-ttu-id="c9455-123">应用为不同的环境（例如，`StartupDevelopment`）单独定义 `Startup` 类时，相应的 `Startup` 类会在运行时被选中。</span><span class="sxs-lookup"><span data-stu-id="c9455-123">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="c9455-124">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="c9455-124">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="c9455-125">如果应用在开发环境中运行并包含 `Startup` 类和 `StartupDevelopment` 类，则使用 `StartupDevelopment` 类。</span><span class="sxs-lookup"><span data-stu-id="c9455-125">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="c9455-126">有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments#environment-based-startup-class-and-methods)。</span><span class="sxs-lookup"><span data-stu-id="c9455-126">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="c9455-127">请参阅[主机](xref:fundamentals/index#host)，了解有关主机的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c9455-127">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="c9455-128">有关在启动过程中处理错误的信息，请参阅[启动异常处理](xref:fundamentals/error-handling#startup-exception-handling)。</span><span class="sxs-lookup"><span data-stu-id="c9455-128">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="c9455-129">ConfigureServices 方法</span><span class="sxs-lookup"><span data-stu-id="c9455-129">The ConfigureServices method</span></span>

<span data-ttu-id="c9455-130"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 方法：</span><span class="sxs-lookup"><span data-stu-id="c9455-130">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="c9455-131">可选。</span><span class="sxs-lookup"><span data-stu-id="c9455-131">Optional.</span></span>
* <span data-ttu-id="c9455-132">在 `Configure` 方法配置应用服务之前，由主机调用。</span><span class="sxs-lookup"><span data-stu-id="c9455-132">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="c9455-133">其中按常规设置[配置选项](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c9455-133">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="c9455-134">主机可能会在调用 `Startup` 方法之前配置某些服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-134">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="c9455-135">有关详细信息，请参阅[主机](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="c9455-135">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="c9455-136">对于需要大量设置的功能，<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 上有 `Add{Service}` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c9455-136">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="c9455-137">例如，AddDbContext、AddDefault:::no-loc(Identity):::、AddEntityFrameworkStores 和 Add:::no-loc(Razor):::Pages   ：</span><span class="sxs-lookup"><span data-stu-id="c9455-137">For example, **Add** DbContext, **Add** Default:::no-loc(Identity):::, **Add** EntityFrameworkStores, and **Add**:::no-loc(Razor):::Pages:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup:::no-loc(Identity):::.cs?name=snippet)]

<span data-ttu-id="c9455-138">将服务添加到服务容器，使其在应用和 `Configure` 方法中可用。</span><span class="sxs-lookup"><span data-stu-id="c9455-138">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="c9455-139">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)或 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> 进行解析。</span><span class="sxs-lookup"><span data-stu-id="c9455-139">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="c9455-140">Configure 方法</span><span class="sxs-lookup"><span data-stu-id="c9455-140">The Configure method</span></span>

<span data-ttu-id="c9455-141"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 方法用于指定应用响应 HTTP 请求的方式。</span><span class="sxs-lookup"><span data-stu-id="c9455-141">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="c9455-142">可通过将[中间件](xref:fundamentals/middleware/index)组件添加到 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 实例来配置请求管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-142">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="c9455-143">`Configure` 方法可使用 `IApplicationBuilder`，但未在服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="c9455-143">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="c9455-144">托管创建 `IApplicationBuilder` 并将其直接传递到 `Configure`。</span><span class="sxs-lookup"><span data-stu-id="c9455-144">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="c9455-145">[ASP.NET Core 模板](/dotnet/core/tools/dotnet-new)配置的管道支持：</span><span class="sxs-lookup"><span data-stu-id="c9455-145">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="c9455-146">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="c9455-146">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="c9455-147">异常处理程序</span><span class="sxs-lookup"><span data-stu-id="c9455-147">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="c9455-148">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="c9455-148">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="c9455-149">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="c9455-149">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="c9455-150">静态文件</span><span class="sxs-lookup"><span data-stu-id="c9455-150">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="c9455-151">ASP.NET Core [MVC](xref:mvc/overview) 和 [:::no-loc(Razor)::: Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="c9455-151">ASP.NET Core [MVC](xref:mvc/overview) and [:::no-loc(Razor)::: Pages](xref:razor-pages/index)</span></span>


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="c9455-152">前面的示例适用于 [:::no-loc(Razor)::: Pages](xref:razor-pages/index)；MVC 版本类似。</span><span class="sxs-lookup"><span data-stu-id="c9455-152">The preceding sample is for [:::no-loc(Razor)::: Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

<span data-ttu-id="c9455-153">每个 `Use` 扩展方法将一个或多个中间件组件添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-153">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="c9455-154">例如，<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> 配置[中间件](xref:fundamentals/middleware/index)提供[静态文件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="c9455-154">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="c9455-155">请求管道中的每个中间件组件负责调用管道中的下一个组件，或在适当情况下使链发生短路。</span><span class="sxs-lookup"><span data-stu-id="c9455-155">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="c9455-156">可以在 `Configure` 方法签名中指定其他服务，如 `IWebHostEnvironment`、`ILoggerFactory` 或 `ConfigureServices` 中定义的任何内容。</span><span class="sxs-lookup"><span data-stu-id="c9455-156">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="c9455-157">如果这些服务可用，则会被注入。</span><span class="sxs-lookup"><span data-stu-id="c9455-157">These services are injected if they're available.</span></span>

<span data-ttu-id="c9455-158">有关如何使用 `IApplicationBuilder` 和中间件处理顺序的详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="c9455-158">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="c9455-159">在不启动的情况下配置服务</span><span class="sxs-lookup"><span data-stu-id="c9455-159">Configure services without Startup</span></span>

<span data-ttu-id="c9455-160">若要配置服务和请求处理管道，而不使用 `Startup` 类，请在主机生成器上调用 `ConfigureServices` 和 `Configure` 便捷方法。</span><span class="sxs-lookup"><span data-stu-id="c9455-160">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="c9455-161">多次调用 `ConfigureServices` 将追加到另一个。</span><span class="sxs-lookup"><span data-stu-id="c9455-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="c9455-162">如果存在多个 `Configure` 方法调用，则使用最后一个 `Configure` 调用。</span><span class="sxs-lookup"><span data-stu-id="c9455-162">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="c9455-163">使用 Startup 筛选器扩展 Startup</span><span class="sxs-lookup"><span data-stu-id="c9455-163">Extend Startup with startup filters</span></span>

<span data-ttu-id="c9455-164">使用 <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>：</span><span class="sxs-lookup"><span data-stu-id="c9455-164">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="c9455-165">在应用的 [Configure](#the-configure-method) 中间件管道的开头或末尾配置中间件，而无需显式调用 `Use{Middleware}`。</span><span class="sxs-lookup"><span data-stu-id="c9455-165">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="c9455-166">`IStartupFilter` 由 ASP.NET Core 用于将默认值添加到管道的开头，而无需使应用作者显式注册默认中间件。</span><span class="sxs-lookup"><span data-stu-id="c9455-166">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="c9455-167">`IStartupFilter` 允许代表应用作者使用不同的组件调用 `Use{Middleware}`。</span><span class="sxs-lookup"><span data-stu-id="c9455-167">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="c9455-168">创建 `Configure` 方法的管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-168">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="c9455-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) 可以将中间件设置为在库添加的中间件之前或之后运行。</span><span class="sxs-lookup"><span data-stu-id="c9455-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="c9455-170">`IStartupFilter` 实现 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>，即接收并返回 `Action<IApplicationBuilder>`。</span><span class="sxs-lookup"><span data-stu-id="c9455-170">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="c9455-171"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 定义用于配置应用请求管道的类。</span><span class="sxs-lookup"><span data-stu-id="c9455-171">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="c9455-172">有关详细信息，请参阅[使用 IApplicationBuilder 创建中间件管道](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="c9455-172">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="c9455-173">每个 `IStartupFilter` 可以在请求管道中添加一个或多个中间件。</span><span class="sxs-lookup"><span data-stu-id="c9455-173">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="c9455-174">筛选器按照添加到服务容器的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="c9455-174">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="c9455-175">筛选器可在将控件传递给下一个筛选器之前或之后添加中间件，从而附加到应用管道的开头或末尾。</span><span class="sxs-lookup"><span data-stu-id="c9455-175">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="c9455-176">下面的示例演示如何使用 `IStartupFilter` 注册中间件。</span><span class="sxs-lookup"><span data-stu-id="c9455-176">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="c9455-177">`RequestSetOptionsMiddleware` 中间件从查询字符串参数中设置选项值：</span><span class="sxs-lookup"><span data-stu-id="c9455-177">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="c9455-178">在 `RequestSetOptionsStartupFilter` 类中配置 `RequestSetOptionsMiddleware`：</span><span class="sxs-lookup"><span data-stu-id="c9455-178">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="c9455-179">在 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 中的服务容器中注册 `IStartupFilter`。</span><span class="sxs-lookup"><span data-stu-id="c9455-179">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

<span data-ttu-id="c9455-180">当提供 `option` 的查询字符串参数时，中间件在 ASP.NET Core 中间件呈现响应之前处理分配值。</span><span class="sxs-lookup"><span data-stu-id="c9455-180">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="c9455-181">中间件执行顺序由 `IStartupFilter` 注册顺序设置：</span><span class="sxs-lookup"><span data-stu-id="c9455-181">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="c9455-182">多个 `IStartupFilter` 实现可能与相同的对象进行交互。</span><span class="sxs-lookup"><span data-stu-id="c9455-182">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="c9455-183">如果顺序很重要，请将它们的 `IStartupFilter` 服务注册进行排序，以匹配其中间件应有的运行顺序。</span><span class="sxs-lookup"><span data-stu-id="c9455-183">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="c9455-184">库可能添加包含一个或多个 `IStartupFilter` 实现的中间件，这些实现在向 `IStartupFilter` 注册的其他应用中间件之前或之后运行。</span><span class="sxs-lookup"><span data-stu-id="c9455-184">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="c9455-185">若要在库的 `IStartupFilter` 添加的中间件之前调用 `IStartupFilter` 中间件，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c9455-185">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="c9455-186">在库添加到服务容器之前定位服务注册。</span><span class="sxs-lookup"><span data-stu-id="c9455-186">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="c9455-187">要在此后调用，请在添加库之后定位服务注册。</span><span class="sxs-lookup"><span data-stu-id="c9455-187">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="c9455-188">在启动时从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="c9455-188">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="c9455-189">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="c9455-189">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="c9455-190">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c9455-190">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9455-191">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9455-191">Additional resources</span></span>

* [<span data-ttu-id="c9455-192">主机</span><span class="sxs-lookup"><span data-stu-id="c9455-192">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="c9455-193">Startup 类</span><span class="sxs-lookup"><span data-stu-id="c9455-193">The Startup class</span></span>

<span data-ttu-id="c9455-194">ASP.NET Core 应用使用 `Startup` 类，按照约定命名为 `Startup`。</span><span class="sxs-lookup"><span data-stu-id="c9455-194">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="c9455-195">`Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="c9455-195">The `Startup` class:</span></span>

* <span data-ttu-id="c9455-196">可选择性地包括 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 方法以配置应用的服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-196">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="c9455-197">服务是一个提供应用功能的可重用组件。</span><span class="sxs-lookup"><span data-stu-id="c9455-197">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="c9455-198">在 `ConfigureServices` 中注册服务，并通过[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 或 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> 在整个应用中使用服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-198">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="c9455-199">包括 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 方法以创建应用的请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-199">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="c9455-200">在应用启动时，ASP.NET Core 运行时会调用 `ConfigureServices` 和 `Configure`：</span><span class="sxs-lookup"><span data-stu-id="c9455-200">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

<span data-ttu-id="c9455-201">在构建应用[主机](xref:fundamentals/index#host)时指定 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="c9455-201">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="c9455-202">通常，通过在主机生成器上调用 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 方法来指定 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="c9455-202">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="c9455-203">主机提供 `Startup` 类构造函数可用的某些服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-203">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="c9455-204">应用通过 `ConfigureServices` 添加其他服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-204">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="c9455-205">然后，主机和应用服务都可以在 `Configure` 和整个应用中使用。</span><span class="sxs-lookup"><span data-stu-id="c9455-205">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="c9455-206">在 `Startup` 类中[注入依赖关系](xref:fundamentals/dependency-injection)的常见用途为注入：</span><span class="sxs-lookup"><span data-stu-id="c9455-206">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="c9455-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 按环境配置服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="c9455-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> 读取配置。</span><span class="sxs-lookup"><span data-stu-id="c9455-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="c9455-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> 在记录器中创建 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="c9455-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="c9455-210">在调用 `Configure` 方法之前，大多数服务都不可用。</span><span class="sxs-lookup"><span data-stu-id="c9455-210">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="c9455-211">多启动</span><span class="sxs-lookup"><span data-stu-id="c9455-211">Multiple Startup</span></span>

<span data-ttu-id="c9455-212">应用为不同的环境（例如，`StartupDevelopment`）单独定义 `Startup` 类时，相应的 `Startup` 类会在运行时被选中。</span><span class="sxs-lookup"><span data-stu-id="c9455-212">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="c9455-213">优先考虑名称后缀与当前环境相匹配的类。</span><span class="sxs-lookup"><span data-stu-id="c9455-213">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="c9455-214">如果应用在开发环境中运行并包含 `Startup` 类和 `StartupDevelopment` 类，则使用 `StartupDevelopment` 类。</span><span class="sxs-lookup"><span data-stu-id="c9455-214">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="c9455-215">有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments#environment-based-startup-class-and-methods)。</span><span class="sxs-lookup"><span data-stu-id="c9455-215">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="c9455-216">请参阅[主机](xref:fundamentals/index#host)，了解有关主机的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c9455-216">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="c9455-217">有关在启动过程中处理错误的信息，请参阅[启动异常处理](xref:fundamentals/error-handling#startup-exception-handling)。</span><span class="sxs-lookup"><span data-stu-id="c9455-217">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="c9455-218">ConfigureServices 方法</span><span class="sxs-lookup"><span data-stu-id="c9455-218">The ConfigureServices method</span></span>

<span data-ttu-id="c9455-219"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 方法：</span><span class="sxs-lookup"><span data-stu-id="c9455-219">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="c9455-220">可选。</span><span class="sxs-lookup"><span data-stu-id="c9455-220">Optional.</span></span>
* <span data-ttu-id="c9455-221">在 `Configure` 方法配置应用服务之前，由主机调用。</span><span class="sxs-lookup"><span data-stu-id="c9455-221">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="c9455-222">其中按常规设置[配置选项](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c9455-222">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="c9455-223">主机可能会在调用 `Startup` 方法之前配置某些服务。</span><span class="sxs-lookup"><span data-stu-id="c9455-223">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="c9455-224">有关详细信息，请参阅[主机](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="c9455-224">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="c9455-225">对于需要大量设置的功能，<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 上有 `Add{Service}` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="c9455-225">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="c9455-226">例如，AddDbContext、AddDefault:::no-loc(Identity):::、AddEntityFrameworkStores 和 Add:::no-loc(Razor):::Pages   ：</span><span class="sxs-lookup"><span data-stu-id="c9455-226">For example, **Add** DbContext, **Add** Default:::no-loc(Identity):::, **Add** EntityFrameworkStores, and **Add**:::no-loc(Razor):::Pages:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="c9455-227">将服务添加到服务容器，使其在应用和 `Configure` 方法中可用。</span><span class="sxs-lookup"><span data-stu-id="c9455-227">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="c9455-228">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)或 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> 进行解析。</span><span class="sxs-lookup"><span data-stu-id="c9455-228">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="c9455-229">若要详细了解 `SetCompatibilityVersion`，请参阅 [SetCompatibilityVersion](xref:mvc/compatibility-version)。</span><span class="sxs-lookup"><span data-stu-id="c9455-229">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="c9455-230">Configure 方法</span><span class="sxs-lookup"><span data-stu-id="c9455-230">The Configure method</span></span>

<span data-ttu-id="c9455-231"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 方法用于指定应用响应 HTTP 请求的方式。</span><span class="sxs-lookup"><span data-stu-id="c9455-231">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="c9455-232">可通过将[中间件](xref:fundamentals/middleware/index)组件添加到 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 实例来配置请求管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-232">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="c9455-233">`Configure` 方法可使用 `IApplicationBuilder`，但未在服务容器中注册。</span><span class="sxs-lookup"><span data-stu-id="c9455-233">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="c9455-234">托管创建 `IApplicationBuilder` 并将其直接传递到 `Configure`。</span><span class="sxs-lookup"><span data-stu-id="c9455-234">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="c9455-235">[ASP.NET Core 模板](/dotnet/core/tools/dotnet-new)配置的管道支持：</span><span class="sxs-lookup"><span data-stu-id="c9455-235">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="c9455-236">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="c9455-236">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="c9455-237">异常处理程序</span><span class="sxs-lookup"><span data-stu-id="c9455-237">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="c9455-238">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="c9455-238">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="c9455-239">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="c9455-239">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="c9455-240">静态文件</span><span class="sxs-lookup"><span data-stu-id="c9455-240">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="c9455-241">ASP.NET Core [MVC](xref:mvc/overview) 和 [:::no-loc(Razor)::: Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="c9455-241">ASP.NET Core [MVC](xref:mvc/overview) and [:::no-loc(Razor)::: Pages](xref:razor-pages/index)</span></span>
* [<span data-ttu-id="c9455-242">一般数据保护条例 (GDPR)</span><span class="sxs-lookup"><span data-stu-id="c9455-242">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="c9455-243">每个 `Use` 扩展方法将一个或多个中间件组件添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-243">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="c9455-244">例如，<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> 配置[中间件](xref:fundamentals/middleware/index)提供[静态文件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="c9455-244">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="c9455-245">请求管道中的每个中间件组件负责调用管道中的下一个组件，或在适当情况下使链发生短路。</span><span class="sxs-lookup"><span data-stu-id="c9455-245">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="c9455-246">可以在 `Configure` 方法签名中指定其他服务，如 `IHostingEnvironment` 和 `ILoggerFactory`，或 `ConfigureServices` 中定义的任何内容。</span><span class="sxs-lookup"><span data-stu-id="c9455-246">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="c9455-247">如果这些服务可用，则会被注入。</span><span class="sxs-lookup"><span data-stu-id="c9455-247">These services are injected if they're available.</span></span>

<span data-ttu-id="c9455-248">有关如何使用 `IApplicationBuilder` 和中间件处理顺序的详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="c9455-248">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="c9455-249">在不启动的情况下配置服务</span><span class="sxs-lookup"><span data-stu-id="c9455-249">Configure services without Startup</span></span>

<span data-ttu-id="c9455-250">若要配置服务和请求处理管道，而不使用 `Startup` 类，请在主机生成器上调用 `ConfigureServices` 和 `Configure` 便捷方法。</span><span class="sxs-lookup"><span data-stu-id="c9455-250">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="c9455-251">多次调用 `ConfigureServices` 将追加到另一个。</span><span class="sxs-lookup"><span data-stu-id="c9455-251">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="c9455-252">如果存在多个 `Configure` 方法调用，则使用最后一个 `Configure` 调用。</span><span class="sxs-lookup"><span data-stu-id="c9455-252">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="c9455-253">使用 Startup 筛选器扩展 Startup</span><span class="sxs-lookup"><span data-stu-id="c9455-253">Extend Startup with startup filters</span></span>

<span data-ttu-id="c9455-254">使用 <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>：</span><span class="sxs-lookup"><span data-stu-id="c9455-254">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="c9455-255">在应用的 [Configure](#the-configure-method) 中间件管道的开头或末尾配置中间件，而无需显式调用 `Use{Middleware}`。</span><span class="sxs-lookup"><span data-stu-id="c9455-255">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="c9455-256">`IStartupFilter` 由 ASP.NET Core 用于将默认值添加到管道的开头，而无需使应用作者显式注册默认中间件。</span><span class="sxs-lookup"><span data-stu-id="c9455-256">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="c9455-257">`IStartupFilter` 允许代表应用作者使用不同的组件调用 `Use{Middleware}`。</span><span class="sxs-lookup"><span data-stu-id="c9455-257">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="c9455-258">创建 `Configure` 方法的管道。</span><span class="sxs-lookup"><span data-stu-id="c9455-258">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="c9455-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) 可以将中间件设置为在库添加的中间件之前或之后运行。</span><span class="sxs-lookup"><span data-stu-id="c9455-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="c9455-260">`IStartupFilter` 实现 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>，即接收并返回 `Action<IApplicationBuilder>`。</span><span class="sxs-lookup"><span data-stu-id="c9455-260">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="c9455-261"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 定义用于配置应用请求管道的类。</span><span class="sxs-lookup"><span data-stu-id="c9455-261">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="c9455-262">有关详细信息，请参阅[使用 IApplicationBuilder 创建中间件管道](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)。</span><span class="sxs-lookup"><span data-stu-id="c9455-262">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="c9455-263">每个 `IStartupFilter` 可以在请求管道中添加一个或多个中间件。</span><span class="sxs-lookup"><span data-stu-id="c9455-263">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="c9455-264">筛选器按照添加到服务容器的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="c9455-264">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="c9455-265">筛选器可在将控件传递给下一个筛选器之前或之后添加中间件，从而附加到应用管道的开头或末尾。</span><span class="sxs-lookup"><span data-stu-id="c9455-265">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="c9455-266">下面的示例演示如何使用 `IStartupFilter` 注册中间件。</span><span class="sxs-lookup"><span data-stu-id="c9455-266">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="c9455-267">`RequestSetOptionsMiddleware` 中间件从查询字符串参数中设置选项值：</span><span class="sxs-lookup"><span data-stu-id="c9455-267">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="c9455-268">在 `RequestSetOptionsStartupFilter` 类中配置 `RequestSetOptionsMiddleware`：</span><span class="sxs-lookup"><span data-stu-id="c9455-268">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="c9455-269">在 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 中的服务容器中注册 `IStartupFilter`。</span><span class="sxs-lookup"><span data-stu-id="c9455-269">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="c9455-270">当提供 `option` 的查询字符串参数时，中间件在 ASP.NET Core 中间件呈现响应之前处理分配值。</span><span class="sxs-lookup"><span data-stu-id="c9455-270">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="c9455-271">中间件执行顺序由 `IStartupFilter` 注册顺序设置：</span><span class="sxs-lookup"><span data-stu-id="c9455-271">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="c9455-272">多个 `IStartupFilter` 实现可能与相同的对象进行交互。</span><span class="sxs-lookup"><span data-stu-id="c9455-272">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="c9455-273">如果顺序很重要，请将它们的 `IStartupFilter` 服务注册进行排序，以匹配其中间件应有的运行顺序。</span><span class="sxs-lookup"><span data-stu-id="c9455-273">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="c9455-274">库可能添加包含一个或多个 `IStartupFilter` 实现的中间件，这些实现在向 `IStartupFilter` 注册的其他应用中间件之前或之后运行。</span><span class="sxs-lookup"><span data-stu-id="c9455-274">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="c9455-275">若要在库的 `IStartupFilter` 添加的中间件之前调用 `IStartupFilter` 中间件，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c9455-275">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="c9455-276">在库添加到服务容器之前定位服务注册。</span><span class="sxs-lookup"><span data-stu-id="c9455-276">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="c9455-277">要在此后调用，请在添加库之后定位服务注册。</span><span class="sxs-lookup"><span data-stu-id="c9455-277">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="c9455-278">在启动时从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="c9455-278">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="c9455-279">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="c9455-279">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="c9455-280">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="c9455-280">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9455-281">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9455-281">Additional resources</span></span>

* [<span data-ttu-id="c9455-282">主机</span><span class="sxs-lookup"><span data-stu-id="c9455-282">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end