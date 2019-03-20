---
title: ASP.NET Core 基础知识
author: rick-anderson
description: 了解生成 ASP.NET Core 应用的基础概念。
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: fundamentals/index
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="59df9-103">ASP.NET Core 基础知识</span><span class="sxs-lookup"><span data-stu-id="59df9-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="59df9-104">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="59df9-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="59df9-105">Startup 类</span><span class="sxs-lookup"><span data-stu-id="59df9-105">The Startup class</span></span>

<span data-ttu-id="59df9-106">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="59df9-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="59df9-107">已配置应用所需的任何服务。</span><span class="sxs-lookup"><span data-stu-id="59df9-107">Any services required by the app are configured.</span></span>
* <span data-ttu-id="59df9-108">已定义请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="59df9-108">The request handling pipeline is defined.</span></span>

* <span data-ttu-id="59df9-109">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="59df9-109">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="59df9-110">服务是应用使用的组件。</span><span class="sxs-lookup"><span data-stu-id="59df9-110">*Services* are components that are used by the app.</span></span> <span data-ttu-id="59df9-111">例如，Entity Framework Core 上下文对象是一项服务。</span><span class="sxs-lookup"><span data-stu-id="59df9-111">For example, an Entity Framework Core context object is a service.</span></span>
* <span data-ttu-id="59df9-112">将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="59df9-112">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span> <span data-ttu-id="59df9-113">管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="59df9-113">The pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="59df9-114">例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="59df9-114">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="59df9-115">每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="59df9-115">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="59df9-116">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="59df9-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

::: moniker-end

<span data-ttu-id="59df9-117">有关详细信息，请参阅[应用启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="59df9-117">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="59df9-118">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="59df9-118">Dependency injection (services)</span></span>

<span data-ttu-id="59df9-119">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。</span><span class="sxs-lookup"><span data-stu-id="59df9-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="59df9-120">在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。</span><span class="sxs-lookup"><span data-stu-id="59df9-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="59df9-121">参数可以是服务类型或接口。</span><span class="sxs-lookup"><span data-stu-id="59df9-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="59df9-122">DI 系统在运行时提供服务。</span><span class="sxs-lookup"><span data-stu-id="59df9-122">The DI system provides the service at runtime.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="59df9-123">下面是使用 DI 获取 Entity Framework Core 上下文对象的类。</span><span class="sxs-lookup"><span data-stu-id="59df9-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="59df9-124">突出显示的行是构造函数注入的示例：</span><span class="sxs-lookup"><span data-stu-id="59df9-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

::: moniker-end

<span data-ttu-id="59df9-125">虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="59df9-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="59df9-126">有关详细信息，请参阅[依存关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="59df9-126">For more information, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware"></a><span data-ttu-id="59df9-127">中间件</span><span class="sxs-lookup"><span data-stu-id="59df9-127">Middleware</span></span>

<span data-ttu-id="59df9-128">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="59df9-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="59df9-129">每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="59df9-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="59df9-130">按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="59df9-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="59df9-131">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="59df9-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="59df9-132">以下示例中突出显示的代码配置请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="59df9-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

::: moniker-end

<span data-ttu-id="59df9-133">ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="59df9-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="59df9-134">有关详细信息，请参阅[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="59df9-134">For more information, see [Middleware](xref:fundamentals/middleware/index).</span></span>

<a id="host"/>

## <a name="the-host"></a><span data-ttu-id="59df9-135">主机</span><span class="sxs-lookup"><span data-stu-id="59df9-135">The host</span></span>

<span data-ttu-id="59df9-136">ASP.NET Core 应用在启动时构建主机。</span><span class="sxs-lookup"><span data-stu-id="59df9-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="59df9-137">主机是封装所有应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="59df9-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="59df9-138">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="59df9-138">An HTTP server implementation</span></span>
* <span data-ttu-id="59df9-139">中间件组件</span><span class="sxs-lookup"><span data-stu-id="59df9-139">Middleware components</span></span>
* <span data-ttu-id="59df9-140">日志记录</span><span class="sxs-lookup"><span data-stu-id="59df9-140">Logging</span></span>
* <span data-ttu-id="59df9-141">DI</span><span class="sxs-lookup"><span data-stu-id="59df9-141">DI</span></span>
* <span data-ttu-id="59df9-142">Configuration</span><span class="sxs-lookup"><span data-stu-id="59df9-142">Configuration</span></span>

<span data-ttu-id="59df9-143">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="59df9-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="59df9-144">创建主机的代码位于 `Program.Main` 中，并遵循[生成器模式](https://wikipedia.org/wiki/Builder_pattern)。</span><span class="sxs-lookup"><span data-stu-id="59df9-144">The code to create a host is in `Program.Main` and follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern).</span></span> <span data-ttu-id="59df9-145">调用方法来配置属于主机的每个资源。</span><span class="sxs-lookup"><span data-stu-id="59df9-145">Methods are called to configure each resource that is part of the host.</span></span> <span data-ttu-id="59df9-146">调用生成器方法以拉取其所有内容并实例化该主机对象。</span><span class="sxs-lookup"><span data-stu-id="59df9-146">A builder method is called to pull it all together and instantiate the host object.</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59df9-147">ASP.NET Core 2.x 使用适用于 Web 应用的 Web 主机（`WebHost` 类）。</span><span class="sxs-lookup"><span data-stu-id="59df9-147">ASP.NET Core 2.x uses Web Host (the `WebHost` class) for web apps.</span></span> <span data-ttu-id="59df9-148">该框架提供了 `CreateDefaultBuilder`，用于设置具有常用选项的主机，例如：</span><span class="sxs-lookup"><span data-stu-id="59df9-148">The framework provides `CreateDefaultBuilder` to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="59df9-149">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="59df9-149">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="59df9-150">从 appsettings.json、环境变量、命令行参数和其他源加载配置。</span><span class="sxs-lookup"><span data-stu-id="59df9-150">Load configuration from *appsettings.json*, environment variables, command line arguments, and other sources.</span></span>
* <span data-ttu-id="59df9-151">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="59df9-151">Send logging output to the console and debug providers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="59df9-152">以下是构建主机的示例代码：</span><span class="sxs-lookup"><span data-stu-id="59df9-152">Here's sample code that builds a host:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

<span data-ttu-id="59df9-153">有关详细信息，请参阅 [Web 主机](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="59df9-153">For more information, see [Web Host](xref:fundamentals/host/web-host).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="59df9-154">在 ASP.NET Core 3.0 中，可在 Web 应用中使用 Web 主机（`WebHost` 类）或泛型主机（`Host` 类）。</span><span class="sxs-lookup"><span data-stu-id="59df9-154">In ASP.NET Core 3.0, Web Host (`WebHost` class) or Generic Host (`Host` class) can be used in a web app.</span></span> <span data-ttu-id="59df9-155">建议使用泛型主机，并且 Web 主机可提供后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="59df9-155">Generic Host is recommended, and Web Host is available for backwards compatibility.</span></span>

<span data-ttu-id="59df9-156">该框架提供了 `CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 方法，用于设置具有常用选项的主机，例如：</span><span class="sxs-lookup"><span data-stu-id="59df9-156">The framework provides the `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="59df9-157">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="59df9-157">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="59df9-158">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数中加载配置。</span><span class="sxs-lookup"><span data-stu-id="59df9-158">Load configuration from *appsettings.json*, *appsettings.[EnvironmentName].json*, environment variables, and command line arguments.</span></span>
* <span data-ttu-id="59df9-159">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="59df9-159">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="59df9-160">以下是构建主机的示例代码。</span><span class="sxs-lookup"><span data-stu-id="59df9-160">Here's sample code that builds a host.</span></span> <span data-ttu-id="59df9-161">使用常用选项设置主机的这些方法已突出显示。</span><span class="sxs-lookup"><span data-stu-id="59df9-161">The methods that set up the host with commonly used options are highlighted.</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

<span data-ttu-id="59df9-162">有关详细信息，请参阅[泛型主机](xref:fundamentals/host/generic-host)和 [Web 主机](xref:fundamentals/host/web-host)</span><span class="sxs-lookup"><span data-stu-id="59df9-162">For more information, see [Generic Host](xref:fundamentals/host/generic-host) and [Web Host](xref:fundamentals/host/web-host)</span></span>

::: moniker-end

### <a name="advanced-host-scenarios"></a><span data-ttu-id="59df9-163">高级主机方案</span><span class="sxs-lookup"><span data-stu-id="59df9-163">Advanced host scenarios</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="59df9-164">Web 主机旨在包含 HTTP 服务器实现，这是其他类型的 .NET 应用所不需要的。</span><span class="sxs-lookup"><span data-stu-id="59df9-164">Web Host is designed to include an HTTP server implementation, which isn't needed for other kinds of .NET apps.</span></span> <span data-ttu-id="59df9-165">自 2.1 起，泛型主机（`Host` 类）可供任何 .NET Core 应用使用，而不仅仅是 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="59df9-165">Starting in 2.1, Generic Host (`Host` class) is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="59df9-166">使用泛型主机，可以在其他类型的应用中使用各种功能（例如，日志记录、DI、配置和应用生命周期管理）。</span><span class="sxs-lookup"><span data-stu-id="59df9-166">Generic Host lets you use cross-cutting features such as logging, DI, configuration, and app lifetime management in other types of apps.</span></span> <span data-ttu-id="59df9-167">有关详细信息，请参阅[泛型主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="59df9-167">For more information, see [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="59df9-168">泛型主机可供任何 .NET Core 应用使用，而不仅仅是 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="59df9-168">Generic Host is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="59df9-169">使用泛型主机，可以在其他类型的应用中使用各种功能（例如，日志记录、DI、配置和应用生命周期管理）。</span><span class="sxs-lookup"><span data-stu-id="59df9-169">Generic Host lets you use cross-cutting features such as logging, DI, configuration, and app lifetime management in other types of apps.</span></span> <span data-ttu-id="59df9-170">有关详细信息，请参阅[泛型主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="59df9-170">For more information, see [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

<span data-ttu-id="59df9-171">还可以使用主机运行后台任务。</span><span class="sxs-lookup"><span data-stu-id="59df9-171">You can also use the host to run background tasks.</span></span> <span data-ttu-id="59df9-172">有关详细信息，请参阅[后台任务](xref:fundamentals/host/hosted-services)。</span><span class="sxs-lookup"><span data-stu-id="59df9-172">For more information, see [Background tasks](xref:fundamentals/host/hosted-services).</span></span>

## <a name="servers"></a><span data-ttu-id="59df9-173">服务器</span><span class="sxs-lookup"><span data-stu-id="59df9-173">Servers</span></span>

<span data-ttu-id="59df9-174">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="59df9-174">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="59df9-175">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="59df9-175">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="59df9-176">Windows</span><span class="sxs-lookup"><span data-stu-id="59df9-176">Windows</span></span>](#tab/windows)

<span data-ttu-id="59df9-177">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="59df9-177">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="59df9-178">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="59df9-178">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="59df9-179">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-179">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="59df9-180">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="59df9-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="59df9-181">IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="59df9-181">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="59df9-182">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-182">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="59df9-183">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="59df9-183">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="59df9-184">macOS</span><span class="sxs-lookup"><span data-stu-id="59df9-184">macOS</span></span>](#tab/macos)

<span data-ttu-id="59df9-185">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="59df9-185">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="59df9-186">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="59df9-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="59df9-187">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-187">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="59df9-188">Linux</span><span class="sxs-lookup"><span data-stu-id="59df9-188">Linux</span></span>](#tab/linux)

<span data-ttu-id="59df9-189">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="59df9-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="59df9-190">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="59df9-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="59df9-191">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="59df9-192">Windows</span><span class="sxs-lookup"><span data-stu-id="59df9-192">Windows</span></span>](#tab/windows)

<span data-ttu-id="59df9-193">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="59df9-193">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="59df9-194">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="59df9-194">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="59df9-195">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-195">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="59df9-196">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="59df9-196">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="59df9-197">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="59df9-197">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="59df9-198">macOS</span><span class="sxs-lookup"><span data-stu-id="59df9-198">macOS</span></span>](#tab/macos)

<span data-ttu-id="59df9-199">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="59df9-199">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="59df9-200">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="59df9-200">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="59df9-201">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-201">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="59df9-202">Linux</span><span class="sxs-lookup"><span data-stu-id="59df9-202">Linux</span></span>](#tab/linux)

<span data-ttu-id="59df9-203">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="59df9-203">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="59df9-204">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="59df9-204">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="59df9-205">Kestrel 通常使用 [Nginx](http://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="59df9-205">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="59df9-206">有关详细信息，请参阅[服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="59df9-206">For more information, see [Servers](xref:fundamentals/servers/index).</span></span>

## <a name="configuration"></a><span data-ttu-id="59df9-207">Configuration</span><span class="sxs-lookup"><span data-stu-id="59df9-207">Configuration</span></span>

<span data-ttu-id="59df9-208">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="59df9-208">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="59df9-209">有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="59df9-209">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="59df9-210">此外可以编写自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="59df9-210">You can also write custom configuration providers.</span></span>

<span data-ttu-id="59df9-211">例如，可以指定配置来自 appsettings.json 和环境变量。</span><span class="sxs-lookup"><span data-stu-id="59df9-211">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="59df9-212">然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中查找。</span><span class="sxs-lookup"><span data-stu-id="59df9-212">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="59df9-213">如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。</span><span class="sxs-lookup"><span data-stu-id="59df9-213">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="59df9-214">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="59df9-214">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="59df9-215">对于生产机密，建议使用 [Azure 密钥保管库](/aspnet/core/security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="59df9-215">For production secrets, we recommend [Azure Key Vault](/aspnet/core/security/key-vault-configuration).</span></span>

<span data-ttu-id="59df9-216">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="59df9-216">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="options"></a><span data-ttu-id="59df9-217">选项</span><span class="sxs-lookup"><span data-stu-id="59df9-217">Options</span></span>

<span data-ttu-id="59df9-218">在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值。</span><span class="sxs-lookup"><span data-stu-id="59df9-218">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="59df9-219">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="59df9-219">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="59df9-220">例如，以下代码可以设置 WebSocket 选项：</span><span class="sxs-lookup"><span data-stu-id="59df9-220">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="59df9-221">有关详细信息，请参阅[选项](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="59df9-221">For more information, see [Options](xref:fundamentals/configuration/options).</span></span>

## <a name="environments"></a><span data-ttu-id="59df9-222">环境</span><span class="sxs-lookup"><span data-stu-id="59df9-222">Environments</span></span>

<span data-ttu-id="59df9-223">执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念。</span><span class="sxs-lookup"><span data-stu-id="59df9-223">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="59df9-224">可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。</span><span class="sxs-lookup"><span data-stu-id="59df9-224">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="59df9-225">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="59df9-225">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="59df9-226">可通过 DI 在应用的任何位置使用环境对象。</span><span class="sxs-lookup"><span data-stu-id="59df9-226">The environment object is available anywhere in the app via DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="59df9-227">以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：</span><span class="sxs-lookup"><span data-stu-id="59df9-227">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

::: moniker-end

<span data-ttu-id="59df9-228">有关详细信息，请参阅[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="59df9-228">For more information, see [Environments](xref:fundamentals/environments).</span></span>

## <a name="logging"></a><span data-ttu-id="59df9-229">日志记录</span><span class="sxs-lookup"><span data-stu-id="59df9-229">Logging</span></span>

<span data-ttu-id="59df9-230">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="59df9-230">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="59df9-231">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="59df9-231">Available providers include the following:</span></span>

* <span data-ttu-id="59df9-232">控制台</span><span class="sxs-lookup"><span data-stu-id="59df9-232">Console</span></span>
* <span data-ttu-id="59df9-233">调试</span><span class="sxs-lookup"><span data-stu-id="59df9-233">Debug</span></span>
* <span data-ttu-id="59df9-234">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="59df9-234">Event Tracing on Windows</span></span>
* <span data-ttu-id="59df9-235">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="59df9-235">Windows Event Log</span></span>
* <span data-ttu-id="59df9-236">TraceSource</span><span class="sxs-lookup"><span data-stu-id="59df9-236">TraceSource</span></span>
* <span data-ttu-id="59df9-237">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="59df9-237">Azure App Service</span></span>
* <span data-ttu-id="59df9-238">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="59df9-238">Azure Application Insights</span></span>

<span data-ttu-id="59df9-239">通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。</span><span class="sxs-lookup"><span data-stu-id="59df9-239">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="59df9-240">下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。</span><span class="sxs-lookup"><span data-stu-id="59df9-240">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

::: moniker-end

<span data-ttu-id="59df9-241">通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="59df9-241">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="59df9-242">字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="59df9-242">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="59df9-243">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="59df9-243">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="59df9-244">有关详细信息，请参阅[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="59df9-244">For more information, see [Logging](xref:fundamentals/logging/index).</span></span>

## <a name="routing"></a><span data-ttu-id="59df9-245">路由</span><span class="sxs-lookup"><span data-stu-id="59df9-245">Routing</span></span>

<span data-ttu-id="59df9-246">路由是映射到处理程序的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="59df9-246">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="59df9-247">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="59df9-247">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="59df9-248">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="59df9-248">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="59df9-249">有关详细信息，请参阅[路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="59df9-249">For more information, see [Routing](xref:fundamentals/routing).</span></span>

## <a name="error-handling"></a><span data-ttu-id="59df9-250">错误处理</span><span class="sxs-lookup"><span data-stu-id="59df9-250">Error handling</span></span>

<span data-ttu-id="59df9-251">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="59df9-251">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="59df9-252">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="59df9-252">A developer exception page</span></span>
* <span data-ttu-id="59df9-253">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="59df9-253">Custom error pages</span></span>
* <span data-ttu-id="59df9-254">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="59df9-254">Static status code pages</span></span>
* <span data-ttu-id="59df9-255">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="59df9-255">Startup exception handling</span></span>

<span data-ttu-id="59df9-256">有关详细信息，请参阅[错误处理](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="59df9-256">For more information, see [Error handling](xref:fundamentals/error-handling).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="make-http-requests"></a><span data-ttu-id="59df9-257">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="59df9-257">Make HTTP requests</span></span>

<span data-ttu-id="59df9-258">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="59df9-258">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="59df9-259">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="59df9-259">The factory:</span></span>

* <span data-ttu-id="59df9-260">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="59df9-260">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="59df9-261">例如，可以注册 github 客户端，并将它配置为访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="59df9-261">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="59df9-262">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="59df9-262">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="59df9-263">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="59df9-263">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="59df9-264">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="59df9-264">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="59df9-265">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="59df9-265">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="59df9-266">与 Polly 集成，这是用于瞬时故障处理的常用第三方库。</span><span class="sxs-lookup"><span data-stu-id="59df9-266">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="59df9-267">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="59df9-267">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="59df9-268">（通过 ILogger）添加可配置的日志记录体验，以处理通过工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="59df9-268">Adds a configurable logging experience (via *ILogger*) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="59df9-269">有关详细信息，请参阅[发出 HTTP 请求](xref:fundamentals/http-requests)。</span><span class="sxs-lookup"><span data-stu-id="59df9-269">For more information, see [Make HTTP requests](xref:fundamentals/http-requests).</span></span>

::: moniker-end

## <a name="content-root"></a><span data-ttu-id="59df9-270">内容根</span><span class="sxs-lookup"><span data-stu-id="59df9-270">Content root</span></span>

<span data-ttu-id="59df9-271">内容根是应用所使用的任何专用内容的基路径，例如 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="59df9-271">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="59df9-272">默认情况下，内容根是托管应用的可执行文件的基路径。</span><span class="sxs-lookup"><span data-stu-id="59df9-272">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="59df9-273">在[构建主机](#host)时，可以指定备用位置。</span><span class="sxs-lookup"><span data-stu-id="59df9-273">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="59df9-274">有关详细信息，请参阅[内容根](xref:fundamentals/host/web-host#content-root)。</span><span class="sxs-lookup"><span data-stu-id="59df9-274">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

<span data-ttu-id="59df9-275">有关详细信息，请参阅[内容根](xref:fundamentals/host/generic-host#content-root)。</span><span class="sxs-lookup"><span data-stu-id="59df9-275">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="59df9-276">Web 根</span><span class="sxs-lookup"><span data-stu-id="59df9-276">Web root</span></span>

<span data-ttu-id="59df9-277">Web 根（也称为 webroot）是公共、静态资源（例如 CSS、JavaScript 和图像文件）的基路径。</span><span class="sxs-lookup"><span data-stu-id="59df9-277">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="59df9-278">默认情况下，静态文件中间件仅提供来自 Web 根目录（及子目录）的文件。</span><span class="sxs-lookup"><span data-stu-id="59df9-278">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="59df9-279">Web 根路径默认为 \<content root>/wwwroot，但[构建主机](#host)时可以指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="59df9-279">The web root path defaults to *\<content root>/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

<span data-ttu-id="59df9-280">在 Razor (.cshtml) 文件中，波浪号斜杠 `~/` 指向 Web 根。</span><span class="sxs-lookup"><span data-stu-id="59df9-280">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="59df9-281">以 `~/` 开头的路径称为虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="59df9-281">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="59df9-282">有关详细信息，请参阅[静态文件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="59df9-282">For more information, see [Static files](xref:fundamentals/static-files).</span></span>
