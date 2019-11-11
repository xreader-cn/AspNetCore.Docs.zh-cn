---
title: ASP.NET Core 基础知识
author: rick-anderson
description: 了解生成 ASP.NET Core 应用的基础概念。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/index
ms.openlocfilehash: 7173a732a04bf3e598adef298fa9120c15dd52fb
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799369"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="0c4c9-103">ASP.NET Core 基础知识</span><span class="sxs-lookup"><span data-stu-id="0c4c9-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="0c4c9-104">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="0c4c9-105">Startup 类</span><span class="sxs-lookup"><span data-stu-id="0c4c9-105">The Startup class</span></span>

<span data-ttu-id="0c4c9-106">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="0c4c9-107">已配置应用所需的服务。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="0c4c9-108">已定义请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="0c4c9-109">服务是应用使用的组件  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="0c4c9-110">例如，日志记录组件就是一项服务。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-110">For example, a logging component is a service.</span></span> <span data-ttu-id="0c4c9-111">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="0c4c9-112">请求处理管道由一系列中间件组件组成  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="0c4c9-113">例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="0c4c9-114">每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="0c4c9-115">将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="0c4c9-116">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="0c4c9-117">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="0c4c9-118">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="0c4c9-118">Dependency injection (services)</span></span>

<span data-ttu-id="0c4c9-119">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="0c4c9-120">在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="0c4c9-121">参数可以是服务类型或接口。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="0c4c9-122">DI 系统在运行时提供服务。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="0c4c9-123">下面是使用 DI 获取 Entity Framework Core 上下文对象的类。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="0c4c9-124">突出显示的行是构造函数注入的示例：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="0c4c9-125">虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="0c4c9-126">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="0c4c9-127">中间件</span><span class="sxs-lookup"><span data-stu-id="0c4c9-127">Middleware</span></span>

<span data-ttu-id="0c4c9-128">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="0c4c9-129">每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="0c4c9-130">按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="0c4c9-131">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="0c4c9-132">以下示例中突出显示的代码配置请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="0c4c9-133">ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="0c4c9-134">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="0c4c9-135">主机</span><span class="sxs-lookup"><span data-stu-id="0c4c9-135">Host</span></span>

<span data-ttu-id="0c4c9-136">ASP.NET Core 应用在启动时构建主机  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="0c4c9-137">主机是封装所有应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="0c4c9-138">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="0c4c9-138">An HTTP server implementation</span></span>
* <span data-ttu-id="0c4c9-139">中间件组件</span><span class="sxs-lookup"><span data-stu-id="0c4c9-139">Middleware components</span></span>
* <span data-ttu-id="0c4c9-140">Logging</span><span class="sxs-lookup"><span data-stu-id="0c4c9-140">Logging</span></span>
* <span data-ttu-id="0c4c9-141">DI</span><span class="sxs-lookup"><span data-stu-id="0c4c9-141">DI</span></span>
* <span data-ttu-id="0c4c9-142">配置</span><span class="sxs-lookup"><span data-stu-id="0c4c9-142">Configuration</span></span>

<span data-ttu-id="0c4c9-143">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c4c9-144">提供两个主机：通用主机和 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="0c4c9-145">通用主机是推荐的机型，而 Web 主机仅很适用于向后兼容。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="0c4c9-146">要创建主机的代码位于 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="0c4c9-147">`CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 方法配置具有常用选项的主机，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="0c4c9-148">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="0c4c9-149">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置   。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="0c4c9-150">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="0c4c9-151">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c4c9-152">提供两个主机：Web 主机和通用主机。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="0c4c9-153">在 ASP.NET Core 2.x 上，通用主机仅用于非 Web 方案。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="0c4c9-154">要创建主机的代码位于 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="0c4c9-155">`CreateDefaultBuilder` 方法配置具有常用选项的主机，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="0c4c9-156">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="0c4c9-157">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置   。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="0c4c9-158">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="0c4c9-159">有关详细信息，请参阅 <xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="0c4c9-160">非 Web 方案</span><span class="sxs-lookup"><span data-stu-id="0c4c9-160">Non-web scenarios</span></span>

<span data-ttu-id="0c4c9-161">其他类型的应用可通过通用主机使用横切框架扩展，例如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="0c4c9-162">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="0c4c9-163">服务器</span><span class="sxs-lookup"><span data-stu-id="0c4c9-163">Servers</span></span>

<span data-ttu-id="0c4c9-164">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="0c4c9-165">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="0c4c9-166">Windows</span><span class="sxs-lookup"><span data-stu-id="0c4c9-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="0c4c9-167">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="0c4c9-168">Kestrel 是跨平台 Web 服务器  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="0c4c9-169">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="0c4c9-170">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="0c4c9-171">IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="0c4c9-172">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="0c4c9-173">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="0c4c9-174">macOS</span><span class="sxs-lookup"><span data-stu-id="0c4c9-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="0c4c9-175">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0c4c9-176">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0c4c9-177">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="0c4c9-178">Linux</span><span class="sxs-lookup"><span data-stu-id="0c4c9-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="0c4c9-179">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0c4c9-180">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0c4c9-181">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="0c4c9-182">Windows</span><span class="sxs-lookup"><span data-stu-id="0c4c9-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="0c4c9-183">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="0c4c9-184">Kestrel 是跨平台 Web 服务器  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="0c4c9-185">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="0c4c9-186">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="0c4c9-187">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="0c4c9-188">macOS</span><span class="sxs-lookup"><span data-stu-id="0c4c9-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="0c4c9-189">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0c4c9-190">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0c4c9-191">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="0c4c9-192">Linux</span><span class="sxs-lookup"><span data-stu-id="0c4c9-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="0c4c9-193">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="0c4c9-194">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="0c4c9-195">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="0c4c9-196">有关详细信息，请参阅 <xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="0c4c9-197">配置</span><span class="sxs-lookup"><span data-stu-id="0c4c9-197">Configuration</span></span>

<span data-ttu-id="0c4c9-198">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="0c4c9-199">有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数   。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="0c4c9-200">此外可以编写自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="0c4c9-201">例如，可以指定配置来自 appsettings.json 和环境变量  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="0c4c9-202">然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中查找   。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="0c4c9-203">如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="0c4c9-204">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0c4c9-205">对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="0c4c9-206">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="0c4c9-207">选项</span><span class="sxs-lookup"><span data-stu-id="0c4c9-207">Options</span></span>

<span data-ttu-id="0c4c9-208">在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="0c4c9-209">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="0c4c9-210">例如，以下代码可以设置 WebSocket 选项：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="0c4c9-211">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="0c4c9-212">环境</span><span class="sxs-lookup"><span data-stu-id="0c4c9-212">Environments</span></span>

<span data-ttu-id="0c4c9-213">执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念    。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="0c4c9-214">可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="0c4c9-215">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="0c4c9-216">可通过 DI 在应用的任何位置使用环境对象。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="0c4c9-217">以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="0c4c9-218">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="0c4c9-219">Logging</span><span class="sxs-lookup"><span data-stu-id="0c4c9-219">Logging</span></span>

<span data-ttu-id="0c4c9-220">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="0c4c9-221">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-221">Available providers include the following:</span></span>

* <span data-ttu-id="0c4c9-222">控制台</span><span class="sxs-lookup"><span data-stu-id="0c4c9-222">Console</span></span>
* <span data-ttu-id="0c4c9-223">调试</span><span class="sxs-lookup"><span data-stu-id="0c4c9-223">Debug</span></span>
* <span data-ttu-id="0c4c9-224">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="0c4c9-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="0c4c9-225">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="0c4c9-225">Windows Event Log</span></span>
* <span data-ttu-id="0c4c9-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="0c4c9-226">TraceSource</span></span>
* <span data-ttu-id="0c4c9-227">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="0c4c9-227">Azure App Service</span></span>
* <span data-ttu-id="0c4c9-228">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="0c4c9-228">Azure Application Insights</span></span>

<span data-ttu-id="0c4c9-229">通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="0c4c9-230">下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="0c4c9-231">通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="0c4c9-232">字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="0c4c9-233">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="0c4c9-234">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="0c4c9-235">路由</span><span class="sxs-lookup"><span data-stu-id="0c4c9-235">Routing</span></span>

<span data-ttu-id="0c4c9-236">路由是映射到处理程序的 URL 模式  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="0c4c9-237">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="0c4c9-238">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="0c4c9-239">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="0c4c9-240">错误处理</span><span class="sxs-lookup"><span data-stu-id="0c4c9-240">Error handling</span></span>

<span data-ttu-id="0c4c9-241">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="0c4c9-242">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="0c4c9-242">A developer exception page</span></span>
* <span data-ttu-id="0c4c9-243">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="0c4c9-243">Custom error pages</span></span>
* <span data-ttu-id="0c4c9-244">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="0c4c9-244">Static status code pages</span></span>
* <span data-ttu-id="0c4c9-245">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="0c4c9-245">Startup exception handling</span></span>

<span data-ttu-id="0c4c9-246">有关详细信息，请参阅 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="0c4c9-247">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="0c4c9-247">Make HTTP requests</span></span>

<span data-ttu-id="0c4c9-248">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="0c4c9-249">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-249">The factory:</span></span>

* <span data-ttu-id="0c4c9-250">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="0c4c9-251">例如，可以注册 github  客户端，并将它配置为访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="0c4c9-252">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="0c4c9-253">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="0c4c9-254">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="0c4c9-255">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="0c4c9-256">与 Polly 集成，这是用于瞬时故障处理的常用第三方库  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="0c4c9-257">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="0c4c9-258">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="0c4c9-259">有关详细信息，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="0c4c9-260">内容根</span><span class="sxs-lookup"><span data-stu-id="0c4c9-260">Content root</span></span>

<span data-ttu-id="0c4c9-261">内容根是指向以下内容的基本路径：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-261">The content root is the base path to the:</span></span>

* <span data-ttu-id="0c4c9-262">托管应用程序的可执行文件 (.exe  )。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-262">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="0c4c9-263">构成应用程序的已编译程序集 (.dll  )。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-263">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="0c4c9-264">应用使用的非代码内容文件，例如：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-264">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="0c4c9-265">Razor 文件（.cshtml  、.razor  ）</span><span class="sxs-lookup"><span data-stu-id="0c4c9-265">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="0c4c9-266">配置文件（.json  、.xml  ）</span><span class="sxs-lookup"><span data-stu-id="0c4c9-266">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="0c4c9-267">数据文件 (.db  )</span><span class="sxs-lookup"><span data-stu-id="0c4c9-267">Data files (*.db*)</span></span>
* <span data-ttu-id="0c4c9-268">[Web 根目录](#web-root)，通常是已发布的 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-268">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="0c4c9-269">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-269">During development:</span></span>

* <span data-ttu-id="0c4c9-270">内容根默认为项目的根目录。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-270">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="0c4c9-271">项目的根目录用于创建：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-271">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="0c4c9-272">项目根目录中应用的非代码内容文件的路径。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-272">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="0c4c9-273">[Web 根目录](#web-root)，通常是项目根目录中的 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-273">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c4c9-274">在[构建主机](#host)时，可以指定备用内容根路径。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-274">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="0c4c9-275">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#contentrootpath>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-275">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c4c9-276">在[构建主机](#host)时，可以指定备用内容根路径。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-276">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="0c4c9-277">有关详细信息，请参阅 <xref:fundamentals/host/web-host#content-root>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-277">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="0c4c9-278">Web 根</span><span class="sxs-lookup"><span data-stu-id="0c4c9-278">Web root</span></span>

<span data-ttu-id="0c4c9-279">Web 根目录是公共、非代码、静态资源文件的基本路径，例如：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-279">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="0c4c9-280">样式表 (.css  )</span><span class="sxs-lookup"><span data-stu-id="0c4c9-280">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="0c4c9-281">JavaScript (.js  )</span><span class="sxs-lookup"><span data-stu-id="0c4c9-281">JavaScript (*.js*)</span></span>
* <span data-ttu-id="0c4c9-282">图像（.png  、.jpg  ）</span><span class="sxs-lookup"><span data-stu-id="0c4c9-282">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="0c4c9-283">默认情况下，静态文件仅从 Web 根目录（及子目录）提供。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-283">Static files are only served by default from the web root directory (and sub-directories).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c4c9-284">Web 根路径默认为 {content root}/wwwroot  ，但[构建主机](#host)时可以指定其他 Web 根路径。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-284">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="0c4c9-285">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#webroot>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-285">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0c4c9-286">Web 根路径默认为 {content root}/wwwroot  ，但[构建主机](#host)时可以指定其他 Web 根路径。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-286">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="0c4c9-287">有关详细信息，请参阅 [Web 根目录](xref:fundamentals/host/web-host#web-root)。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-287">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

::: moniker-end

<span data-ttu-id="0c4c9-288">防止使用项目文件中的 [\<Content> 项目项](/visualstudio/msbuild/common-msbuild-project-items#content)在 wwwroot 中发布文件  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-288">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="0c4c9-289">下面的示例会阻止在 wwwroot/local 目录和子目录中发布内容  ：</span><span class="sxs-lookup"><span data-stu-id="0c4c9-289">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="0c4c9-290">在 Razor (.cshtml  ) 文件中，波浪号斜杠 (`~/`) 指向 Web 根目录。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-290">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="0c4c9-291">以 `~/` 开头的路径称为虚拟路径  。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-291">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="0c4c9-292">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="0c4c9-292">For more information, see <xref:fundamentals/static-files>.</span></span>
