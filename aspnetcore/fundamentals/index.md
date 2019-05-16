---
title: ASP.NET Core 基础知识
author: rick-anderson
description: 了解生成 ASP.NET Core 应用的基础概念。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: fundamentals/index
ms.openlocfilehash: 9c7bc25d813ad17825ef03f5176882993cc2dd63
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610327"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="f5f64-103">ASP.NET Core 基础知识</span><span class="sxs-lookup"><span data-stu-id="f5f64-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="f5f64-104">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="f5f64-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="f5f64-105">Startup 类</span><span class="sxs-lookup"><span data-stu-id="f5f64-105">The Startup class</span></span>

<span data-ttu-id="f5f64-106">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="f5f64-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="f5f64-107">已配置应用所需的任何服务。</span><span class="sxs-lookup"><span data-stu-id="f5f64-107">Any services required by the app are configured.</span></span>
* <span data-ttu-id="f5f64-108">已定义请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="f5f64-108">The request handling pipeline is defined.</span></span>

* <span data-ttu-id="f5f64-109">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="f5f64-109">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f5f64-110">服务是应用使用的组件。</span><span class="sxs-lookup"><span data-stu-id="f5f64-110">*Services* are components that are used by the app.</span></span> <span data-ttu-id="f5f64-111">例如，Entity Framework Core 上下文对象是一项服务。</span><span class="sxs-lookup"><span data-stu-id="f5f64-111">For example, an Entity Framework Core context object is a service.</span></span>
* <span data-ttu-id="f5f64-112">将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="f5f64-112">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span> <span data-ttu-id="f5f64-113">管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="f5f64-113">The pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="f5f64-114">例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f5f64-114">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="f5f64-115">每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="f5f64-115">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="f5f64-116">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="f5f64-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="f5f64-117">有关更多信息，请参见<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="f5f64-118">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="f5f64-118">Dependency injection (services)</span></span>

<span data-ttu-id="f5f64-119">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。</span><span class="sxs-lookup"><span data-stu-id="f5f64-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="f5f64-120">在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。</span><span class="sxs-lookup"><span data-stu-id="f5f64-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="f5f64-121">参数可以是服务类型或接口。</span><span class="sxs-lookup"><span data-stu-id="f5f64-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="f5f64-122">DI 系统在运行时提供服务。</span><span class="sxs-lookup"><span data-stu-id="f5f64-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="f5f64-123">下面是使用 DI 获取 Entity Framework Core 上下文对象的类。</span><span class="sxs-lookup"><span data-stu-id="f5f64-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="f5f64-124">突出显示的行是构造函数注入的示例：</span><span class="sxs-lookup"><span data-stu-id="f5f64-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="f5f64-125">虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="f5f64-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="f5f64-126">有关更多信息，请参见<xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="f5f64-127">中间件</span><span class="sxs-lookup"><span data-stu-id="f5f64-127">Middleware</span></span>

<span data-ttu-id="f5f64-128">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="f5f64-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="f5f64-129">每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="f5f64-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="f5f64-130">按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="f5f64-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="f5f64-131">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="f5f64-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="f5f64-132">以下示例中突出显示的代码配置请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="f5f64-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="f5f64-133">ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="f5f64-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="f5f64-134">有关更多信息，请参见<xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<a id="host"/>

## <a name="the-host"></a><span data-ttu-id="f5f64-135">主机</span><span class="sxs-lookup"><span data-stu-id="f5f64-135">The host</span></span>

<span data-ttu-id="f5f64-136">ASP.NET Core 应用在启动时构建主机。</span><span class="sxs-lookup"><span data-stu-id="f5f64-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="f5f64-137">主机是封装所有应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="f5f64-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="f5f64-138">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="f5f64-138">An HTTP server implementation</span></span>
* <span data-ttu-id="f5f64-139">中间件组件</span><span class="sxs-lookup"><span data-stu-id="f5f64-139">Middleware components</span></span>
* <span data-ttu-id="f5f64-140">日志记录</span><span class="sxs-lookup"><span data-stu-id="f5f64-140">Logging</span></span>
* <span data-ttu-id="f5f64-141">DI</span><span class="sxs-lookup"><span data-stu-id="f5f64-141">DI</span></span>
* <span data-ttu-id="f5f64-142">Configuration</span><span class="sxs-lookup"><span data-stu-id="f5f64-142">Configuration</span></span>

<span data-ttu-id="f5f64-143">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="f5f64-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="f5f64-144">创建主机的代码位于 `Program.Main` 中，并遵循[生成器模式](https://wikipedia.org/wiki/Builder_pattern)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-144">The code to create a host is in `Program.Main` and follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern).</span></span> <span data-ttu-id="f5f64-145">调用方法来配置属于主机的每个资源。</span><span class="sxs-lookup"><span data-stu-id="f5f64-145">Methods are called to configure each resource that is part of the host.</span></span> <span data-ttu-id="f5f64-146">调用生成器方法以拉取其所有内容并实例化该主机对象。</span><span class="sxs-lookup"><span data-stu-id="f5f64-146">A builder method is called to pull it all together and instantiate the host object.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5f64-147">`CreateHostBuilder` 是向外部组件（如[实体框架](/ef/core/)）标识生成器方法的特殊名称。</span><span class="sxs-lookup"><span data-stu-id="f5f64-147">`CreateHostBuilder` is special name that identifies the builder method to external components, such as [Entity Framework](/ef/core/).</span></span>

<span data-ttu-id="f5f64-148">在 ASP.NET Core 3.0 或更高版本中，可在 Web 应用中使用泛型主机（`Host` 类）或 Web 主机（`WebHost` 类）。</span><span class="sxs-lookup"><span data-stu-id="f5f64-148">In ASP.NET Core 3.0 or later, Generic Host (`Host` class) or Web Host (`WebHost` class) can be used in a web app.</span></span> <span data-ttu-id="f5f64-149">建议使用泛型主机，并且 Web 主机可提供后向兼容性。</span><span class="sxs-lookup"><span data-stu-id="f5f64-149">Generic Host is recommended, and Web Host is available for backwards compatibility.</span></span>

<span data-ttu-id="f5f64-150">该框架提供了 `CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 方法，用于设置具有常用选项的主机，例如：</span><span class="sxs-lookup"><span data-stu-id="f5f64-150">The framework provides the `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="f5f64-151">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="f5f64-151">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="f5f64-152">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置。</span><span class="sxs-lookup"><span data-stu-id="f5f64-152">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="f5f64-153">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="f5f64-153">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="f5f64-154">以下是构建主机的示例代码。</span><span class="sxs-lookup"><span data-stu-id="f5f64-154">Here's sample code that builds a host.</span></span> <span data-ttu-id="f5f64-155">使用常用选项设置主机的方法已突出显示：</span><span class="sxs-lookup"><span data-stu-id="f5f64-155">The methods that set up the host with commonly used options are highlighted:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

<span data-ttu-id="f5f64-156">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-156">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5f64-157">`CreateWebHostBuilder` 是向外部组件（如[实体框架](/ef/core/)）标识生成器方法的特殊名称。</span><span class="sxs-lookup"><span data-stu-id="f5f64-157">`CreateWebHostBuilder` is special name that identifies the builder method to external components, such as [Entity Framework](/ef/core/).</span></span>

<span data-ttu-id="f5f64-158">ASP.NET Core 2.x 对 Web 应用使用 Web 主机（`WebHost` 类）。</span><span class="sxs-lookup"><span data-stu-id="f5f64-158">ASP.NET Core 2.x uses Web Host (`WebHost` class) for web apps.</span></span> <span data-ttu-id="f5f64-159">该框架提供了 `CreateDefaultBuilder`，用于设置具有常用选项的主机，例如：</span><span class="sxs-lookup"><span data-stu-id="f5f64-159">The framework provides `CreateDefaultBuilder` to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="f5f64-160">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="f5f64-160">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="f5f64-161">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置。</span><span class="sxs-lookup"><span data-stu-id="f5f64-161">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="f5f64-162">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="f5f64-162">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="f5f64-163">以下是构建主机的示例代码：</span><span class="sxs-lookup"><span data-stu-id="f5f64-163">Here's sample code that builds a host:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

<span data-ttu-id="f5f64-164">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-164">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="advanced-host-scenarios"></a><span data-ttu-id="f5f64-165">高级主机方案</span><span class="sxs-lookup"><span data-stu-id="f5f64-165">Advanced host scenarios</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5f64-166">泛型主机可供任何 .NET Core 应用使用，而不仅仅是 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f5f64-166">Generic Host is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="f5f64-167">使用泛型主机（`Host` 类），其他类型的应用可以使用各种框架扩展（例如，日志记录、DI、配置和应用生命周期管理）。</span><span class="sxs-lookup"><span data-stu-id="f5f64-167">Generic Host (`Host` class) allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="f5f64-168">有关更多信息，请参见<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5f64-169">Web 主机旨在包含 HTTP 服务器实现，这是其他类型的 .NET 应用所不需要的。</span><span class="sxs-lookup"><span data-stu-id="f5f64-169">Web Host is designed to include an HTTP server implementation, which isn't required for other kinds of .NET apps.</span></span> <span data-ttu-id="f5f64-170">自 ASP.NET Core 2.1 起，泛型主机（`Host` 类）可供任何 .NET Core 应用使用，而不仅仅是 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f5f64-170">Starting in ASP.NET Core 2.1, the Generic Host (`Host` class) is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="f5f64-171">使用泛型主机，其他类型的应用可以使用各种框架扩展（例如，日志记录、DI、配置和应用生命周期管理）。</span><span class="sxs-lookup"><span data-stu-id="f5f64-171">Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="f5f64-172">有关更多信息，请参见<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

<span data-ttu-id="f5f64-173">还可以使用主机运行后台任务。</span><span class="sxs-lookup"><span data-stu-id="f5f64-173">You can also use the host to run background tasks.</span></span> <span data-ttu-id="f5f64-174">有关更多信息，请参见<xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-174">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="f5f64-175">服务器</span><span class="sxs-lookup"><span data-stu-id="f5f64-175">Servers</span></span>

<span data-ttu-id="f5f64-176">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="f5f64-176">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="f5f64-177">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-177">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="f5f64-178">Windows</span><span class="sxs-lookup"><span data-stu-id="f5f64-178">Windows</span></span>](#tab/windows)

<span data-ttu-id="f5f64-179">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="f5f64-179">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f5f64-180">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="f5f64-180">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f5f64-181">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-181">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f5f64-182">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="f5f64-182">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f5f64-183">IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="f5f64-183">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="f5f64-184">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-184">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="f5f64-185">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="f5f64-185">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="f5f64-186">macOS</span><span class="sxs-lookup"><span data-stu-id="f5f64-186">macOS</span></span>](#tab/macos)

<span data-ttu-id="f5f64-187">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f5f64-187">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f5f64-188">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="f5f64-188">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f5f64-189">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-189">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="f5f64-190">Linux</span><span class="sxs-lookup"><span data-stu-id="f5f64-190">Linux</span></span>](#tab/linux)

<span data-ttu-id="f5f64-191">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f5f64-191">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f5f64-192">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="f5f64-192">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f5f64-193">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-193">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="f5f64-194">Windows</span><span class="sxs-lookup"><span data-stu-id="f5f64-194">Windows</span></span>](#tab/windows)

<span data-ttu-id="f5f64-195">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="f5f64-195">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f5f64-196">Kestrel 是跨平台 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="f5f64-196">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f5f64-197">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-197">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f5f64-198">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="f5f64-198">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f5f64-199">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器。</span><span class="sxs-lookup"><span data-stu-id="f5f64-199">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="f5f64-200">macOS</span><span class="sxs-lookup"><span data-stu-id="f5f64-200">macOS</span></span>](#tab/macos)

<span data-ttu-id="f5f64-201">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f5f64-201">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f5f64-202">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="f5f64-202">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f5f64-203">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-203">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="f5f64-204">Linux</span><span class="sxs-lookup"><span data-stu-id="f5f64-204">Linux</span></span>](#tab/linux)

<span data-ttu-id="f5f64-205">ASP.NET Core 提供 Kestrel 跨平台服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f5f64-205">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f5f64-206">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="f5f64-206">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f5f64-207">Kestrel 通常使用 [Nginx](http://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="f5f64-207">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="f5f64-208">有关更多信息，请参见<xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-208">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="f5f64-209">Configuration</span><span class="sxs-lookup"><span data-stu-id="f5f64-209">Configuration</span></span>

<span data-ttu-id="f5f64-210">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="f5f64-210">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="f5f64-211">有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f5f64-211">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="f5f64-212">此外可以编写自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="f5f64-212">You can also write custom configuration providers.</span></span>

<span data-ttu-id="f5f64-213">例如，可以指定配置来自 appsettings.json 和环境变量。</span><span class="sxs-lookup"><span data-stu-id="f5f64-213">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="f5f64-214">然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中查找。</span><span class="sxs-lookup"><span data-stu-id="f5f64-214">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="f5f64-215">如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。</span><span class="sxs-lookup"><span data-stu-id="f5f64-215">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="f5f64-216">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-216">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f5f64-217">对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-217">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="f5f64-218">有关更多信息，请参见<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-218">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="f5f64-219">选项</span><span class="sxs-lookup"><span data-stu-id="f5f64-219">Options</span></span>

<span data-ttu-id="f5f64-220">在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值。</span><span class="sxs-lookup"><span data-stu-id="f5f64-220">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="f5f64-221">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="f5f64-221">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="f5f64-222">例如，以下代码可以设置 WebSocket 选项：</span><span class="sxs-lookup"><span data-stu-id="f5f64-222">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="f5f64-223">有关更多信息，请参见<xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-223">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="f5f64-224">环境</span><span class="sxs-lookup"><span data-stu-id="f5f64-224">Environments</span></span>

<span data-ttu-id="f5f64-225">执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念。</span><span class="sxs-lookup"><span data-stu-id="f5f64-225">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="f5f64-226">可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。</span><span class="sxs-lookup"><span data-stu-id="f5f64-226">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="f5f64-227">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="f5f64-227">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="f5f64-228">可通过 DI 在应用的任何位置使用环境对象。</span><span class="sxs-lookup"><span data-stu-id="f5f64-228">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="f5f64-229">以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：</span><span class="sxs-lookup"><span data-stu-id="f5f64-229">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="f5f64-230">有关更多信息，请参见<xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-230">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="f5f64-231">日志记录</span><span class="sxs-lookup"><span data-stu-id="f5f64-231">Logging</span></span>

<span data-ttu-id="f5f64-232">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="f5f64-232">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f5f64-233">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="f5f64-233">Available providers include the following:</span></span>

* <span data-ttu-id="f5f64-234">控制台</span><span class="sxs-lookup"><span data-stu-id="f5f64-234">Console</span></span>
* <span data-ttu-id="f5f64-235">调试</span><span class="sxs-lookup"><span data-stu-id="f5f64-235">Debug</span></span>
* <span data-ttu-id="f5f64-236">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="f5f64-236">Event Tracing on Windows</span></span>
* <span data-ttu-id="f5f64-237">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="f5f64-237">Windows Event Log</span></span>
* <span data-ttu-id="f5f64-238">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f5f64-238">TraceSource</span></span>
* <span data-ttu-id="f5f64-239">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="f5f64-239">Azure App Service</span></span>
* <span data-ttu-id="f5f64-240">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="f5f64-240">Azure Application Insights</span></span>

<span data-ttu-id="f5f64-241">通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。</span><span class="sxs-lookup"><span data-stu-id="f5f64-241">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="f5f64-242">下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。</span><span class="sxs-lookup"><span data-stu-id="f5f64-242">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="f5f64-243">通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="f5f64-243">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="f5f64-244">字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="f5f64-244">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="f5f64-245">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-245">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f5f64-246">有关更多信息，请参见<xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-246">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="f5f64-247">路由</span><span class="sxs-lookup"><span data-stu-id="f5f64-247">Routing</span></span>

<span data-ttu-id="f5f64-248">路由是映射到处理程序的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="f5f64-248">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="f5f64-249">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="f5f64-249">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="f5f64-250">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="f5f64-250">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="f5f64-251">有关更多信息，请参见<xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-251">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="f5f64-252">错误处理</span><span class="sxs-lookup"><span data-stu-id="f5f64-252">Error handling</span></span>

<span data-ttu-id="f5f64-253">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="f5f64-253">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="f5f64-254">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="f5f64-254">A developer exception page</span></span>
* <span data-ttu-id="f5f64-255">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="f5f64-255">Custom error pages</span></span>
* <span data-ttu-id="f5f64-256">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="f5f64-256">Static status code pages</span></span>
* <span data-ttu-id="f5f64-257">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="f5f64-257">Startup exception handling</span></span>

<span data-ttu-id="f5f64-258">有关更多信息，请参见<xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-258">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="f5f64-259">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="f5f64-259">Make HTTP requests</span></span>

<span data-ttu-id="f5f64-260">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="f5f64-260">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="f5f64-261">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="f5f64-261">The factory:</span></span>

* <span data-ttu-id="f5f64-262">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="f5f64-262">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f5f64-263">例如，可以注册 github 客户端，并将它配置为访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="f5f64-263">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="f5f64-264">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="f5f64-264">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f5f64-265">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="f5f64-265">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f5f64-266">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="f5f64-266">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f5f64-267">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="f5f64-267">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="f5f64-268">与 Polly 集成，这是用于瞬时故障处理的常用第三方库。</span><span class="sxs-lookup"><span data-stu-id="f5f64-268">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="f5f64-269">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="f5f64-269">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f5f64-270">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="f5f64-270">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f5f64-271">有关更多信息，请参见<xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-271">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="f5f64-272">内容根</span><span class="sxs-lookup"><span data-stu-id="f5f64-272">Content root</span></span>

<span data-ttu-id="f5f64-273">内容根是应用所使用的任何专用内容的基路径，例如 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="f5f64-273">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="f5f64-274">默认情况下，内容根是托管应用的可执行文件的基路径。</span><span class="sxs-lookup"><span data-stu-id="f5f64-274">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="f5f64-275">在[构建主机](#host)时，可以指定备用位置。</span><span class="sxs-lookup"><span data-stu-id="f5f64-275">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5f64-276">有关详细信息，请参阅[内容根](xref:fundamentals/host/generic-host#content-root)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-276">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5f64-277">有关详细信息，请参阅[内容根](xref:fundamentals/host/web-host#content-root)。</span><span class="sxs-lookup"><span data-stu-id="f5f64-277">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="f5f64-278">Web 根</span><span class="sxs-lookup"><span data-stu-id="f5f64-278">Web root</span></span>

<span data-ttu-id="f5f64-279">Web 根（也称为 webroot）是公共、静态资源（例如 CSS、JavaScript 和图像文件）的基路径。</span><span class="sxs-lookup"><span data-stu-id="f5f64-279">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="f5f64-280">默认情况下，静态文件中间件仅提供来自 Web 根目录（及子目录）的文件。</span><span class="sxs-lookup"><span data-stu-id="f5f64-280">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="f5f64-281">Web 根路径默认为 {Content Root}/wwwroot，但[构建主机](#host)时可以指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="f5f64-281">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

<span data-ttu-id="f5f64-282">在 Razor (.cshtml) 文件中，波浪号斜杠 `~/` 指向 Web 根。</span><span class="sxs-lookup"><span data-stu-id="f5f64-282">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="f5f64-283">以 `~/` 开头的路径称为虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="f5f64-283">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="f5f64-284">有关更多信息，请参见<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="f5f64-284">For more information, see <xref:fundamentals/static-files>.</span></span>
