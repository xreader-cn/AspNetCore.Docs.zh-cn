---
title: ASP.NET Core 基础知识
author: rick-anderson
description: 了解生成 ASP.NET Core 应用的基础概念。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 25348f8486ec6ccb53ebf527ad4519638dd5f73e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059372"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="8bd11-103">ASP.NET Core 基础知识</span><span class="sxs-lookup"><span data-stu-id="8bd11-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8bd11-104">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="8bd11-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="8bd11-105">Startup 类</span><span class="sxs-lookup"><span data-stu-id="8bd11-105">The Startup class</span></span>

<span data-ttu-id="8bd11-106">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="8bd11-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="8bd11-107">已配置应用所需的服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="8bd11-108">应用的请求处理管道定义为一系列中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="8bd11-109">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="8bd11-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="8bd11-110">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="8bd11-111">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="8bd11-111">Dependency injection (services)</span></span>

<span data-ttu-id="8bd11-112">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可在应用中提供配置的服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="8bd11-113">例如，日志记录组件就是一项服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="8bd11-114">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="8bd11-114">Code to configure (or *register* ) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8bd11-115">例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="8bd11-116">通常使用构造函数注入从 DI 解析服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="8bd11-117">通过构造函数注入，有一个类声明请求的类型或接口的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="8bd11-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="8bd11-118">DI 框架在运行时提供此服务的实例。</span><span class="sxs-lookup"><span data-stu-id="8bd11-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="8bd11-119">以下示例使用构造函数注入从 DI 解析 `RazorPagesMovieContext`：</span><span class="sxs-lookup"><span data-stu-id="8bd11-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="8bd11-120">如果内置控制反转 (IoC) 容器不能满足应用的所有需求，可以改用第三方 IoC。</span><span class="sxs-lookup"><span data-stu-id="8bd11-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="8bd11-121">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="8bd11-122">中间件</span><span class="sxs-lookup"><span data-stu-id="8bd11-122">Middleware</span></span>

<span data-ttu-id="8bd11-123">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="8bd11-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="8bd11-124">每个组件在 `HttpContext` 上执行操作，调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="8bd11-125">按照惯例，通过在 `Startup.Configure` 方法中调用 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="8bd11-126">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="8bd11-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="8bd11-127">以下示例配置了请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="8bd11-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="8bd11-128">ASP.NET Core 包含一组丰富的内置中间件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="8bd11-129">也可编写自定义中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="8bd11-130">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="8bd11-131">主机</span><span class="sxs-lookup"><span data-stu-id="8bd11-131">Host</span></span>

<span data-ttu-id="8bd11-132">ASP.NET Core 应用在启动时构建主机。</span><span class="sxs-lookup"><span data-stu-id="8bd11-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="8bd11-133">主机封装应用的所有资源，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="8bd11-134">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="8bd11-134">An HTTP server implementation</span></span>
* <span data-ttu-id="8bd11-135">中间件组件</span><span class="sxs-lookup"><span data-stu-id="8bd11-135">Middleware components</span></span>
* <span data-ttu-id="8bd11-136">Logging</span><span class="sxs-lookup"><span data-stu-id="8bd11-136">Logging</span></span>
* <span data-ttu-id="8bd11-137">依赖关系注入 (DI) 服务</span><span class="sxs-lookup"><span data-stu-id="8bd11-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="8bd11-138">Configuration</span><span class="sxs-lookup"><span data-stu-id="8bd11-138">Configuration</span></span>

<span data-ttu-id="8bd11-139">有两个不同的主机：</span><span class="sxs-lookup"><span data-stu-id="8bd11-139">There are two different hosts:</span></span> 

* <span data-ttu-id="8bd11-140">.NET 通用主机</span><span class="sxs-lookup"><span data-stu-id="8bd11-140">.NET Generic Host</span></span>
* <span data-ttu-id="8bd11-141">ASP.NET Core Web 主机</span><span class="sxs-lookup"><span data-stu-id="8bd11-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="8bd11-142">建议使用 .NET 通用主机。</span><span class="sxs-lookup"><span data-stu-id="8bd11-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="8bd11-143">ASP.NET Core Web 主机仅用于支持后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="8bd11-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="8bd11-144">以下示例将创建 .NET 通用主机：</span><span class="sxs-lookup"><span data-stu-id="8bd11-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="8bd11-145">`CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 方法为主机配置一组默认选项，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="8bd11-146">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="8bd11-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="8bd11-147">从 appsettings.json、appsettings.{Environment Name}.json、环境变量、命令行参数和其他配置源中加载配置 。</span><span class="sxs-lookup"><span data-stu-id="8bd11-147">Load configuration from *appsettings.json* , *appsettings.{Environment Name}.json* , environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="8bd11-148">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="8bd11-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="8bd11-149">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="8bd11-150">非 Web 方案</span><span class="sxs-lookup"><span data-stu-id="8bd11-150">Non-web scenarios</span></span>

<span data-ttu-id="8bd11-151">其他类型的应用可通过通用主机使用横切框架扩展，例如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。</span><span class="sxs-lookup"><span data-stu-id="8bd11-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="8bd11-152">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="8bd11-153">服务器</span><span class="sxs-lookup"><span data-stu-id="8bd11-153">Servers</span></span>

<span data-ttu-id="8bd11-154">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="8bd11-155">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="8bd11-156">Windows</span><span class="sxs-lookup"><span data-stu-id="8bd11-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="8bd11-157">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="8bd11-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="8bd11-158">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="8bd11-159">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="8bd11-160">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="8bd11-161">IIS HTTP 服务器适用于使用 IIS 的 Windows。</span><span class="sxs-lookup"><span data-stu-id="8bd11-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="8bd11-162">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="8bd11-163">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8bd11-164">macOS</span><span class="sxs-lookup"><span data-stu-id="8bd11-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="8bd11-165">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="8bd11-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8bd11-166">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8bd11-167">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="8bd11-168">Linux</span><span class="sxs-lookup"><span data-stu-id="8bd11-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="8bd11-169">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="8bd11-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8bd11-170">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8bd11-171">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="8bd11-172">有关详细信息，请参阅 <xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="8bd11-173">Configuration</span><span class="sxs-lookup"><span data-stu-id="8bd11-173">Configuration</span></span>

<span data-ttu-id="8bd11-174">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="8bd11-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="8bd11-175">可将内置配置提供程序用于各种源，例如 .json 文件、.xml 文件、环境变量和命令行参数 。</span><span class="sxs-lookup"><span data-stu-id="8bd11-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="8bd11-176">可编写自定义配置提供程序以支持其他源。</span><span class="sxs-lookup"><span data-stu-id="8bd11-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="8bd11-177">[默认情况下](xref:fundamentals/configuration/index#default)，ASP.NET Core 应用配置为从 appsettings.json、环境变量和命令行等读取内容。</span><span class="sxs-lookup"><span data-stu-id="8bd11-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json* , environment variables, the command line, and more.</span></span> <span data-ttu-id="8bd11-178">加载应用配置后，来自环境变量的值将替代来自 appsettings.json 的值。</span><span class="sxs-lookup"><span data-stu-id="8bd11-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="8bd11-179">读取相关配置值的首选方法是使用[选项模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8bd11-180">有关详细信息，请参阅[使用选项模式绑定分层配置数据](xref:fundamentals/configuration/index#optpat)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="8bd11-181">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器](xref:security/app-secrets#secret-manager)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="8bd11-182">对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="8bd11-183">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="8bd11-184">环境</span><span class="sxs-lookup"><span data-stu-id="8bd11-184">Environments</span></span>

<span data-ttu-id="8bd11-185">执行环境（例如 `Development`、`Staging` 和 `Production`）是 ASP.NET Core 中的高级概念。</span><span class="sxs-lookup"><span data-stu-id="8bd11-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="8bd11-186">通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定应用的运行环境。</span><span class="sxs-lookup"><span data-stu-id="8bd11-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="8bd11-187">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IWebHostEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="8bd11-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="8bd11-188">通过依赖关系注入 (DI)，可以在应用中任何位置实现此操作。</span><span class="sxs-lookup"><span data-stu-id="8bd11-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="8bd11-189">使用以下示例配置应用，应用在 `Development` 环境中运行时将提供详细错误信息：</span><span class="sxs-lookup"><span data-stu-id="8bd11-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="8bd11-190">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="8bd11-191">Logging</span><span class="sxs-lookup"><span data-stu-id="8bd11-191">Logging</span></span>

<span data-ttu-id="8bd11-192">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="8bd11-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="8bd11-193">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="8bd11-193">Available providers include:</span></span>

* <span data-ttu-id="8bd11-194">控制台</span><span class="sxs-lookup"><span data-stu-id="8bd11-194">Console</span></span>
* <span data-ttu-id="8bd11-195">调试</span><span class="sxs-lookup"><span data-stu-id="8bd11-195">Debug</span></span>
* <span data-ttu-id="8bd11-196">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="8bd11-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="8bd11-197">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="8bd11-197">Windows Event Log</span></span>
* <span data-ttu-id="8bd11-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="8bd11-198">TraceSource</span></span>
* <span data-ttu-id="8bd11-199">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="8bd11-199">Azure App Service</span></span>
* <span data-ttu-id="8bd11-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="8bd11-200">Azure Application Insights</span></span>

<span data-ttu-id="8bd11-201">若要创建服务，请从依赖关系注入 (DI) 解析 <xref:Microsoft.Extensions.Logging.ILogger%601> 服务，并调用 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 等日志记录方法。</span><span class="sxs-lookup"><span data-stu-id="8bd11-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="8bd11-202">例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="8bd11-203">`LogInformation` 等日志记录方法支持任意数量的字段。</span><span class="sxs-lookup"><span data-stu-id="8bd11-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="8bd11-204">这些字段通常用于构造消息 `string`，但某些日志记录提供程序会将它们作为独立字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="8bd11-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="8bd11-205">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="8bd11-206">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="8bd11-207">路由</span><span class="sxs-lookup"><span data-stu-id="8bd11-207">Routing</span></span>

<span data-ttu-id="8bd11-208">路由是映射到处理程序的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="8bd11-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="8bd11-209">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="8bd11-210">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="8bd11-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="8bd11-211">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="8bd11-212">错误处理</span><span class="sxs-lookup"><span data-stu-id="8bd11-212">Error handling</span></span>

<span data-ttu-id="8bd11-213">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="8bd11-214">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="8bd11-214">A developer exception page</span></span>
* <span data-ttu-id="8bd11-215">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="8bd11-215">Custom error pages</span></span>
* <span data-ttu-id="8bd11-216">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="8bd11-216">Static status code pages</span></span>
* <span data-ttu-id="8bd11-217">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="8bd11-217">Startup exception handling</span></span>

<span data-ttu-id="8bd11-218">有关详细信息，请参阅 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="8bd11-219">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="8bd11-219">Make HTTP requests</span></span>

<span data-ttu-id="8bd11-220">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="8bd11-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="8bd11-221">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="8bd11-221">The factory:</span></span>

* <span data-ttu-id="8bd11-222">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="8bd11-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="8bd11-223">例如，注册并配置 *github* 客户端以访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="8bd11-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="8bd11-224">注册并配置默认客户端以实现其他目的。</span><span class="sxs-lookup"><span data-stu-id="8bd11-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="8bd11-225">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="8bd11-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="8bd11-226">此模式类似于 ASP.NET Core 的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="8bd11-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="8bd11-227">此模式提供了一种用于管理 HTTP 请求相关问题的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="8bd11-228">与 Polly 集成，这是用于瞬时故障处理的常用第三方库。</span><span class="sxs-lookup"><span data-stu-id="8bd11-228">Integrates with *Polly* , a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="8bd11-229">管理基础 `HttpClientHandler` 实例的池和生存期，避免手动管理 `HttpClient` 生存期时可能出现的常见 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="8bd11-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="8bd11-230">通过 <xref:Microsoft.Extensions.Logging.ILogger> 添加可配置的日志记录体验，用于记录通过工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="8bd11-231">有关详细信息，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="8bd11-232">内容根</span><span class="sxs-lookup"><span data-stu-id="8bd11-232">Content root</span></span>

<span data-ttu-id="8bd11-233">内容根目录是指向以下内容的基路径：</span><span class="sxs-lookup"><span data-stu-id="8bd11-233">The content root is the base path for:</span></span>

* <span data-ttu-id="8bd11-234">托管应用的可执行文件 (.exe)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-234">The executable hosting the app ( *.exe* ).</span></span>
* <span data-ttu-id="8bd11-235">构成应用程序的已编译程序集 (.dll)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-235">Compiled assemblies that make up the app ( *.dll* ).</span></span>
* <span data-ttu-id="8bd11-236">应用使用的内容文件，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="8bd11-237">Razor 文件（.cshtml、.razor） </span><span class="sxs-lookup"><span data-stu-id="8bd11-237">Razor files ( *.cshtml* , *.razor* )</span></span>
  * <span data-ttu-id="8bd11-238">配置文件（.json、.xml）</span><span class="sxs-lookup"><span data-stu-id="8bd11-238">Configuration files ( *.json* , *.xml* )</span></span>
  * <span data-ttu-id="8bd11-239">数据文件 (.db)</span><span class="sxs-lookup"><span data-stu-id="8bd11-239">Data files ( *.db* )</span></span>
* <span data-ttu-id="8bd11-240">[Web 根目录](#web-root)，通常是 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8bd11-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="8bd11-241">在开发中，内容根目录默认为项目的根目录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="8bd11-242">此目录还是应用内容文件和 [Web 根目录](#web-root)的基路径。</span><span class="sxs-lookup"><span data-stu-id="8bd11-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="8bd11-243">在[构建主机](#host)时设置路径，可指定不同的内容根目录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="8bd11-244">有关详细信息，请参阅[内容根](xref:fundamentals/host/generic-host#contentroot)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="8bd11-245">Web 根</span><span class="sxs-lookup"><span data-stu-id="8bd11-245">Web root</span></span>

<span data-ttu-id="8bd11-246">Web 根目录是公用静态资源文件的基路径，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="8bd11-247">样式表 (.css)</span><span class="sxs-lookup"><span data-stu-id="8bd11-247">Stylesheets ( *.css* )</span></span>
* <span data-ttu-id="8bd11-248">JavaScript (.js)</span><span class="sxs-lookup"><span data-stu-id="8bd11-248">JavaScript ( *.js* )</span></span>
* <span data-ttu-id="8bd11-249">图像（.png、.jpg）</span><span class="sxs-lookup"><span data-stu-id="8bd11-249">Images ( *.png* , *.jpg* )</span></span>

<span data-ttu-id="8bd11-250">默认情况下，静态文件仅从 Web 根目录及其子目录提供。</span><span class="sxs-lookup"><span data-stu-id="8bd11-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="8bd11-251">Web 根目录路径默认为 *{content root}/wwwroot* 。</span><span class="sxs-lookup"><span data-stu-id="8bd11-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="8bd11-252">在[构建主机](#host)时设置路径，可指定不同的 Web 根目录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="8bd11-253">有关详细信息，请参阅 [Web 根目录](xref:fundamentals/host/generic-host#webroot)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="8bd11-254">防止使用项目文件中的 [\<Content> 项目项](/visualstudio/msbuild/common-msbuild-project-items#content)在 wwwroot 中发布文件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="8bd11-255">下面的示例会阻止在 wwwroot/local 及其子目录中发布内容：</span><span class="sxs-lookup"><span data-stu-id="8bd11-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="8bd11-256">在 Razor .cshtml 文件中，波形符-斜线 (`~/`) 指向 Web 根。</span><span class="sxs-lookup"><span data-stu-id="8bd11-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="8bd11-257">以 `~/` 开头的路径称为虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="8bd11-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="8bd11-258">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bd11-259">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="8bd11-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="8bd11-260">Startup 类</span><span class="sxs-lookup"><span data-stu-id="8bd11-260">The Startup class</span></span>

<span data-ttu-id="8bd11-261">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="8bd11-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="8bd11-262">已配置应用所需的服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="8bd11-263">已定义请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="8bd11-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="8bd11-264">服务是应用使用的组件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="8bd11-265">例如，日志记录组件就是一项服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-265">For example, a logging component is a service.</span></span> <span data-ttu-id="8bd11-266">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="8bd11-266">Code to configure (or *register* ) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="8bd11-267">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="8bd11-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="8bd11-268">例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8bd11-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="8bd11-269">每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="8bd11-270">将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="8bd11-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="8bd11-271">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="8bd11-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="8bd11-272">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="8bd11-273">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="8bd11-273">Dependency injection (services)</span></span>

<span data-ttu-id="8bd11-274">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。</span><span class="sxs-lookup"><span data-stu-id="8bd11-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="8bd11-275">在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。</span><span class="sxs-lookup"><span data-stu-id="8bd11-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="8bd11-276">参数可以是服务类型或接口。</span><span class="sxs-lookup"><span data-stu-id="8bd11-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="8bd11-277">DI 系统在运行时提供服务。</span><span class="sxs-lookup"><span data-stu-id="8bd11-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="8bd11-278">下面是使用 DI 获取 Entity Framework Core 上下文对象的类。</span><span class="sxs-lookup"><span data-stu-id="8bd11-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="8bd11-279">突出显示的行是构造函数注入的示例：</span><span class="sxs-lookup"><span data-stu-id="8bd11-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="8bd11-280">虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="8bd11-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="8bd11-281">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="8bd11-282">中间件</span><span class="sxs-lookup"><span data-stu-id="8bd11-282">Middleware</span></span>

<span data-ttu-id="8bd11-283">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="8bd11-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="8bd11-284">每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="8bd11-285">按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="8bd11-286">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="8bd11-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="8bd11-287">以下示例中突出显示的代码配置请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="8bd11-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="8bd11-288">ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="8bd11-289">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="8bd11-290">主机</span><span class="sxs-lookup"><span data-stu-id="8bd11-290">Host</span></span>

<span data-ttu-id="8bd11-291">ASP.NET Core 应用在启动时构建主机。</span><span class="sxs-lookup"><span data-stu-id="8bd11-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="8bd11-292">主机是封装所有应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="8bd11-293">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="8bd11-293">An HTTP server implementation</span></span>
* <span data-ttu-id="8bd11-294">中间件组件</span><span class="sxs-lookup"><span data-stu-id="8bd11-294">Middleware components</span></span>
* <span data-ttu-id="8bd11-295">Logging</span><span class="sxs-lookup"><span data-stu-id="8bd11-295">Logging</span></span>
* <span data-ttu-id="8bd11-296">DI</span><span class="sxs-lookup"><span data-stu-id="8bd11-296">DI</span></span>
* <span data-ttu-id="8bd11-297">Configuration</span><span class="sxs-lookup"><span data-stu-id="8bd11-297">Configuration</span></span>

<span data-ttu-id="8bd11-298">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="8bd11-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="8bd11-299">提供两个主机：Web 主机和通用主机。</span><span class="sxs-lookup"><span data-stu-id="8bd11-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="8bd11-300">在 ASP.NET Core 2.x 上，通用主机仅用于非 Web 方案。</span><span class="sxs-lookup"><span data-stu-id="8bd11-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="8bd11-301">要创建主机的代码位于 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8bd11-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="8bd11-302">`CreateDefaultBuilder` 方法配置具有常用选项的主机，如下所示：</span><span class="sxs-lookup"><span data-stu-id="8bd11-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="8bd11-303">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="8bd11-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="8bd11-304">从 appsettings.json、appsettings.{Environment Name}.json、环境变量、命令行参数和其他配置源中加载配置 。</span><span class="sxs-lookup"><span data-stu-id="8bd11-304">Load configuration from *appsettings.json* , *appsettings.{Environment Name}.json* , environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="8bd11-305">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="8bd11-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="8bd11-306">有关详细信息，请参阅 <xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="8bd11-307">非 Web 方案</span><span class="sxs-lookup"><span data-stu-id="8bd11-307">Non-web scenarios</span></span>

<span data-ttu-id="8bd11-308">其他类型的应用可通过通用主机使用横切框架扩展，例如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。</span><span class="sxs-lookup"><span data-stu-id="8bd11-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="8bd11-309">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="8bd11-310">服务器</span><span class="sxs-lookup"><span data-stu-id="8bd11-310">Servers</span></span>

<span data-ttu-id="8bd11-311">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="8bd11-312">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="8bd11-313">Windows</span><span class="sxs-lookup"><span data-stu-id="8bd11-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="8bd11-314">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="8bd11-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="8bd11-315">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="8bd11-316">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="8bd11-317">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="8bd11-318">IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="8bd11-319">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="8bd11-320">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8bd11-321">macOS</span><span class="sxs-lookup"><span data-stu-id="8bd11-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="8bd11-322">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="8bd11-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8bd11-323">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8bd11-324">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="8bd11-325">Linux</span><span class="sxs-lookup"><span data-stu-id="8bd11-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="8bd11-326">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="8bd11-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8bd11-327">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8bd11-328">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="8bd11-329">Windows</span><span class="sxs-lookup"><span data-stu-id="8bd11-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="8bd11-330">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="8bd11-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="8bd11-331">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="8bd11-332">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="8bd11-333">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="8bd11-334">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd11-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8bd11-335">macOS</span><span class="sxs-lookup"><span data-stu-id="8bd11-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="8bd11-336">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="8bd11-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8bd11-337">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8bd11-338">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="8bd11-339">Linux</span><span class="sxs-lookup"><span data-stu-id="8bd11-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="8bd11-340">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="8bd11-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8bd11-341">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="8bd11-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8bd11-342">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="8bd11-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8bd11-343">有关详细信息，请参阅 <xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="8bd11-344">Configuration</span><span class="sxs-lookup"><span data-stu-id="8bd11-344">Configuration</span></span>

<span data-ttu-id="8bd11-345">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="8bd11-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="8bd11-346">有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数 。</span><span class="sxs-lookup"><span data-stu-id="8bd11-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="8bd11-347">此外可以编写自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="8bd11-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="8bd11-348">例如，可指定配置来自 appsettings.json 和环境变量。</span><span class="sxs-lookup"><span data-stu-id="8bd11-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="8bd11-349">然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中进行查找 。</span><span class="sxs-lookup"><span data-stu-id="8bd11-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="8bd11-350">如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。</span><span class="sxs-lookup"><span data-stu-id="8bd11-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="8bd11-351">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8bd11-352">对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="8bd11-353">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="8bd11-354">选项</span><span class="sxs-lookup"><span data-stu-id="8bd11-354">Options</span></span>

<span data-ttu-id="8bd11-355">在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值。</span><span class="sxs-lookup"><span data-stu-id="8bd11-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="8bd11-356">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="8bd11-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="8bd11-357">例如，以下代码可以设置 WebSocket 选项：</span><span class="sxs-lookup"><span data-stu-id="8bd11-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="8bd11-358">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="8bd11-359">环境</span><span class="sxs-lookup"><span data-stu-id="8bd11-359">Environments</span></span>

<span data-ttu-id="8bd11-360">执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念  。</span><span class="sxs-lookup"><span data-stu-id="8bd11-360">Execution environments, such as *Development* , *Staging* , and *Production* , are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="8bd11-361">可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。</span><span class="sxs-lookup"><span data-stu-id="8bd11-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="8bd11-362">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="8bd11-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="8bd11-363">可通过 DI 在应用的任何位置使用环境对象。</span><span class="sxs-lookup"><span data-stu-id="8bd11-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="8bd11-364">以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：</span><span class="sxs-lookup"><span data-stu-id="8bd11-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="8bd11-365">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="8bd11-366">Logging</span><span class="sxs-lookup"><span data-stu-id="8bd11-366">Logging</span></span>

<span data-ttu-id="8bd11-367">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="8bd11-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="8bd11-368">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="8bd11-368">Available providers include the following:</span></span>

* <span data-ttu-id="8bd11-369">控制台</span><span class="sxs-lookup"><span data-stu-id="8bd11-369">Console</span></span>
* <span data-ttu-id="8bd11-370">调试</span><span class="sxs-lookup"><span data-stu-id="8bd11-370">Debug</span></span>
* <span data-ttu-id="8bd11-371">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="8bd11-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="8bd11-372">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="8bd11-372">Windows Event Log</span></span>
* <span data-ttu-id="8bd11-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="8bd11-373">TraceSource</span></span>
* <span data-ttu-id="8bd11-374">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="8bd11-374">Azure App Service</span></span>
* <span data-ttu-id="8bd11-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="8bd11-375">Azure Application Insights</span></span>

<span data-ttu-id="8bd11-376">通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。</span><span class="sxs-lookup"><span data-stu-id="8bd11-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="8bd11-377">下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。</span><span class="sxs-lookup"><span data-stu-id="8bd11-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="8bd11-378">通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="8bd11-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="8bd11-379">字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="8bd11-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="8bd11-380">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="8bd11-381">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="8bd11-382">路由</span><span class="sxs-lookup"><span data-stu-id="8bd11-382">Routing</span></span>

<span data-ttu-id="8bd11-383">路由是映射到处理程序的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="8bd11-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="8bd11-384">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="8bd11-385">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="8bd11-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="8bd11-386">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="8bd11-387">错误处理</span><span class="sxs-lookup"><span data-stu-id="8bd11-387">Error handling</span></span>

<span data-ttu-id="8bd11-388">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="8bd11-389">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="8bd11-389">A developer exception page</span></span>
* <span data-ttu-id="8bd11-390">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="8bd11-390">Custom error pages</span></span>
* <span data-ttu-id="8bd11-391">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="8bd11-391">Static status code pages</span></span>
* <span data-ttu-id="8bd11-392">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="8bd11-392">Startup exception handling</span></span>

<span data-ttu-id="8bd11-393">有关详细信息，请参阅 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="8bd11-394">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="8bd11-394">Make HTTP requests</span></span>

<span data-ttu-id="8bd11-395">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="8bd11-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="8bd11-396">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="8bd11-396">The factory:</span></span>

* <span data-ttu-id="8bd11-397">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="8bd11-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="8bd11-398">例如，可以注册 github 客户端，并将它配置为访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="8bd11-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="8bd11-399">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="8bd11-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="8bd11-400">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="8bd11-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="8bd11-401">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="8bd11-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="8bd11-402">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="8bd11-403">与 Polly 集成，这是用于瞬时故障处理的常用第三方库。</span><span class="sxs-lookup"><span data-stu-id="8bd11-403">Integrates with *Polly* , a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="8bd11-404">管理基础 `HttpClientHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="8bd11-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="8bd11-405">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="8bd11-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="8bd11-406">有关详细信息，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="8bd11-407">内容根</span><span class="sxs-lookup"><span data-stu-id="8bd11-407">Content root</span></span>

<span data-ttu-id="8bd11-408">内容根是指向以下内容的基本路径：</span><span class="sxs-lookup"><span data-stu-id="8bd11-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="8bd11-409">托管应用程序的可执行文件 (.exe)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-409">Executable hosting the app ( *.exe* ).</span></span>
* <span data-ttu-id="8bd11-410">构成应用程序的已编译程序集 (.dll)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-410">Compiled assemblies that make up the app ( *.dll* ).</span></span>
* <span data-ttu-id="8bd11-411">应用使用的非代码内容文件，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="8bd11-412">Razor 文件（.cshtml、.razor） </span><span class="sxs-lookup"><span data-stu-id="8bd11-412">Razor files ( *.cshtml* , *.razor* )</span></span>
  * <span data-ttu-id="8bd11-413">配置文件（.json、.xml）</span><span class="sxs-lookup"><span data-stu-id="8bd11-413">Configuration files ( *.json* , *.xml* )</span></span>
  * <span data-ttu-id="8bd11-414">数据文件 (.db)</span><span class="sxs-lookup"><span data-stu-id="8bd11-414">Data files ( *.db* )</span></span>
* <span data-ttu-id="8bd11-415">[Web 根目录](#web-root)，通常是已发布的 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8bd11-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="8bd11-416">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="8bd11-416">During development:</span></span>

* <span data-ttu-id="8bd11-417">内容根默认为项目的根目录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="8bd11-418">项目的根目录用于创建：</span><span class="sxs-lookup"><span data-stu-id="8bd11-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="8bd11-419">项目根目录中应用的非代码内容文件的路径。</span><span class="sxs-lookup"><span data-stu-id="8bd11-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="8bd11-420">[Web 根目录](#web-root)，通常是项目根目录中的 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="8bd11-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="8bd11-421">在[构建主机](#host)时，可以指定备用内容根路径。</span><span class="sxs-lookup"><span data-stu-id="8bd11-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="8bd11-422">有关详细信息，请参阅 <xref:fundamentals/host/web-host#content-root>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="8bd11-423">Web 根</span><span class="sxs-lookup"><span data-stu-id="8bd11-423">Web root</span></span>

<span data-ttu-id="8bd11-424">Web 根目录是公共、非代码、静态资源文件的基本路径，例如：</span><span class="sxs-lookup"><span data-stu-id="8bd11-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="8bd11-425">样式表 (.css)</span><span class="sxs-lookup"><span data-stu-id="8bd11-425">Stylesheets ( *.css* )</span></span>
* <span data-ttu-id="8bd11-426">JavaScript (.js)</span><span class="sxs-lookup"><span data-stu-id="8bd11-426">JavaScript ( *.js* )</span></span>
* <span data-ttu-id="8bd11-427">图像（.png、.jpg）</span><span class="sxs-lookup"><span data-stu-id="8bd11-427">Images ( *.png* , *.jpg* )</span></span>

<span data-ttu-id="8bd11-428">默认情况下，静态文件仅从 Web 根目录（及子目录）提供。</span><span class="sxs-lookup"><span data-stu-id="8bd11-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="8bd11-429">Web 根路径默认为 {content root}/wwwroot，但[构建主机](#host)时可以指定其他 Web 根路径。</span><span class="sxs-lookup"><span data-stu-id="8bd11-429">The web root path defaults to *{content root}/wwwroot* , but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="8bd11-430">有关详细信息，请参阅 [Web 根目录](xref:fundamentals/host/web-host#web-root)。</span><span class="sxs-lookup"><span data-stu-id="8bd11-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="8bd11-431">防止使用项目文件中的 [\<Content> 项目项](/visualstudio/msbuild/common-msbuild-project-items#content)在 wwwroot 中发布文件。</span><span class="sxs-lookup"><span data-stu-id="8bd11-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="8bd11-432">下面的示例会阻止在 wwwroot/local 目录和子目录中发布内容：</span><span class="sxs-lookup"><span data-stu-id="8bd11-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="8bd11-433">在 Razor (.cshtml) 文件中，波浪号斜杠 (`~/`) 指向 Web 根目录。</span><span class="sxs-lookup"><span data-stu-id="8bd11-433">In Razor ( *.cshtml* ) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="8bd11-434">以 `~/` 开头的路径称为虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="8bd11-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="8bd11-435">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8bd11-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
