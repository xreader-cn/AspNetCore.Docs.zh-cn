---
title: ASP.NET Core 基础知识
author: rick-anderson
description: 了解生成 ASP.NET Core 应用的基础概念。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: a16a2fbb4ad2a79f96b6646ffdc359619d361a25
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434312"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="a82d2-103">ASP.NET Core 基础知识</span><span class="sxs-lookup"><span data-stu-id="a82d2-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a82d2-104">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="a82d2-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="a82d2-105">Startup 类</span><span class="sxs-lookup"><span data-stu-id="a82d2-105">The Startup class</span></span>

<span data-ttu-id="a82d2-106">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="a82d2-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="a82d2-107">已配置应用所需的服务。</span><span class="sxs-lookup"><span data-stu-id="a82d2-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="a82d2-108">已定义请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="a82d2-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="a82d2-109">服务是应用使用的组件  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="a82d2-110">例如，日志记录组件就是一项服务。</span><span class="sxs-lookup"><span data-stu-id="a82d2-110">For example, a logging component is a service.</span></span> <span data-ttu-id="a82d2-111">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="a82d2-112">请求处理管道由一系列中间件组件组成  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="a82d2-113">例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a82d2-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="a82d2-114">每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="a82d2-115">将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="a82d2-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="a82d2-116">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="a82d2-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="a82d2-117">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="a82d2-118">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="a82d2-118">Dependency injection (services)</span></span>

<span data-ttu-id="a82d2-119">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。</span><span class="sxs-lookup"><span data-stu-id="a82d2-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="a82d2-120">在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。</span><span class="sxs-lookup"><span data-stu-id="a82d2-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="a82d2-121">参数可以是服务类型或接口。</span><span class="sxs-lookup"><span data-stu-id="a82d2-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="a82d2-122">DI 系统在运行时提供服务。</span><span class="sxs-lookup"><span data-stu-id="a82d2-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="a82d2-123">下面是使用 DI 获取 Entity Framework Core 上下文对象的类。</span><span class="sxs-lookup"><span data-stu-id="a82d2-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="a82d2-124">突出显示的行是构造函数注入的示例：</span><span class="sxs-lookup"><span data-stu-id="a82d2-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="a82d2-125">虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="a82d2-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="a82d2-126">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="a82d2-127">中间件</span><span class="sxs-lookup"><span data-stu-id="a82d2-127">Middleware</span></span>

<span data-ttu-id="a82d2-128">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="a82d2-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="a82d2-129">每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="a82d2-130">按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="a82d2-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="a82d2-131">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="a82d2-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="a82d2-132">以下示例中突出显示的代码配置请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="a82d2-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="a82d2-133">ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="a82d2-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="a82d2-134">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="a82d2-135">主机</span><span class="sxs-lookup"><span data-stu-id="a82d2-135">Host</span></span>

<span data-ttu-id="a82d2-136">ASP.NET Core 应用在启动时构建主机  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="a82d2-137">主机是封装所有应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="a82d2-138">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="a82d2-138">An HTTP server implementation</span></span>
* <span data-ttu-id="a82d2-139">中间件组件</span><span class="sxs-lookup"><span data-stu-id="a82d2-139">Middleware components</span></span>
* <span data-ttu-id="a82d2-140">Logging</span><span class="sxs-lookup"><span data-stu-id="a82d2-140">Logging</span></span>
* <span data-ttu-id="a82d2-141">DI</span><span class="sxs-lookup"><span data-stu-id="a82d2-141">DI</span></span>
* <span data-ttu-id="a82d2-142">Configuration</span><span class="sxs-lookup"><span data-stu-id="a82d2-142">Configuration</span></span>

<span data-ttu-id="a82d2-143">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="a82d2-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="a82d2-144">提供两个主机：通用主机和 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="a82d2-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="a82d2-145">通用主机是推荐的机型，而 Web 主机仅很适用于向后兼容。</span><span class="sxs-lookup"><span data-stu-id="a82d2-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="a82d2-146">要创建主机的代码位于 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="a82d2-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="a82d2-147">`CreateDefaultBuilder` 和 `ConfigureWebHostDefaults` 方法配置具有常用选项的主机，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a82d2-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="a82d2-148">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="a82d2-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="a82d2-149">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置   。</span><span class="sxs-lookup"><span data-stu-id="a82d2-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="a82d2-150">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="a82d2-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="a82d2-151">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="a82d2-152">非 Web 方案</span><span class="sxs-lookup"><span data-stu-id="a82d2-152">Non-web scenarios</span></span>

<span data-ttu-id="a82d2-153">其他类型的应用可通过通用主机使用横切框架扩展，例如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。</span><span class="sxs-lookup"><span data-stu-id="a82d2-153">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="a82d2-154">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-154">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="a82d2-155">服务器</span><span class="sxs-lookup"><span data-stu-id="a82d2-155">Servers</span></span>

<span data-ttu-id="a82d2-156">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-156">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="a82d2-157">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-157">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="a82d2-158">Windows</span><span class="sxs-lookup"><span data-stu-id="a82d2-158">Windows</span></span>](#tab/windows)

<span data-ttu-id="a82d2-159">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="a82d2-159">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="a82d2-160">Kestrel 是跨平台 Web 服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-160">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="a82d2-161">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-161">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="a82d2-162">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-162">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="a82d2-163">IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-163">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="a82d2-164">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-164">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="a82d2-165">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-165">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="a82d2-166">macOS</span><span class="sxs-lookup"><span data-stu-id="a82d2-166">macOS</span></span>](#tab/macos)

<span data-ttu-id="a82d2-167">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-167">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a82d2-168">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-168">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a82d2-169">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-169">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="a82d2-170">Linux</span><span class="sxs-lookup"><span data-stu-id="a82d2-170">Linux</span></span>](#tab/linux)

<span data-ttu-id="a82d2-171">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-171">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a82d2-172">在 ASP.NET Core 2.0 或更高版本中，Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-172">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a82d2-173">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-173">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="a82d2-174">有关详细信息，请参阅 <xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-174">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="a82d2-175">Configuration</span><span class="sxs-lookup"><span data-stu-id="a82d2-175">Configuration</span></span>

<span data-ttu-id="a82d2-176">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="a82d2-176">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="a82d2-177">有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数   。</span><span class="sxs-lookup"><span data-stu-id="a82d2-177">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="a82d2-178">此外可以编写自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a82d2-178">You can also write custom configuration providers.</span></span>

<span data-ttu-id="a82d2-179">例如，可以指定配置来自 appsettings.json 和环境变量  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-179">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="a82d2-180">然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中查找   。</span><span class="sxs-lookup"><span data-stu-id="a82d2-180">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="a82d2-181">如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。</span><span class="sxs-lookup"><span data-stu-id="a82d2-181">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="a82d2-182">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-182">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a82d2-183">对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-183">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="a82d2-184">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="a82d2-185">选项</span><span class="sxs-lookup"><span data-stu-id="a82d2-185">Options</span></span>

<span data-ttu-id="a82d2-186">在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-186">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="a82d2-187">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="a82d2-187">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="a82d2-188">例如，以下代码可以设置 WebSocket 选项：</span><span class="sxs-lookup"><span data-stu-id="a82d2-188">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="a82d2-189">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-189">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="a82d2-190">环境</span><span class="sxs-lookup"><span data-stu-id="a82d2-190">Environments</span></span>

<span data-ttu-id="a82d2-191">执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念    。</span><span class="sxs-lookup"><span data-stu-id="a82d2-191">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="a82d2-192">可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。</span><span class="sxs-lookup"><span data-stu-id="a82d2-192">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="a82d2-193">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="a82d2-193">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="a82d2-194">可通过 DI 在应用的任何位置使用环境对象。</span><span class="sxs-lookup"><span data-stu-id="a82d2-194">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="a82d2-195">以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：</span><span class="sxs-lookup"><span data-stu-id="a82d2-195">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="a82d2-196">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-196">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="a82d2-197">Logging</span><span class="sxs-lookup"><span data-stu-id="a82d2-197">Logging</span></span>

<span data-ttu-id="a82d2-198">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="a82d2-198">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="a82d2-199">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="a82d2-199">Available providers include the following:</span></span>

* <span data-ttu-id="a82d2-200">控制台</span><span class="sxs-lookup"><span data-stu-id="a82d2-200">Console</span></span>
* <span data-ttu-id="a82d2-201">调试</span><span class="sxs-lookup"><span data-stu-id="a82d2-201">Debug</span></span>
* <span data-ttu-id="a82d2-202">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="a82d2-202">Event Tracing on Windows</span></span>
* <span data-ttu-id="a82d2-203">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="a82d2-203">Windows Event Log</span></span>
* <span data-ttu-id="a82d2-204">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a82d2-204">TraceSource</span></span>
* <span data-ttu-id="a82d2-205">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="a82d2-205">Azure App Service</span></span>
* <span data-ttu-id="a82d2-206">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="a82d2-206">Azure Application Insights</span></span>

<span data-ttu-id="a82d2-207">通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。</span><span class="sxs-lookup"><span data-stu-id="a82d2-207">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="a82d2-208">下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。</span><span class="sxs-lookup"><span data-stu-id="a82d2-208">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="a82d2-209">通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="a82d2-209">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="a82d2-210">字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="a82d2-210">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="a82d2-211">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-211">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="a82d2-212">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="a82d2-213">路由</span><span class="sxs-lookup"><span data-stu-id="a82d2-213">Routing</span></span>

<span data-ttu-id="a82d2-214">路由是映射到处理程序的 URL 模式  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-214">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="a82d2-215">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="a82d2-215">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="a82d2-216">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="a82d2-216">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="a82d2-217">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="a82d2-218">错误处理</span><span class="sxs-lookup"><span data-stu-id="a82d2-218">Error handling</span></span>

<span data-ttu-id="a82d2-219">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-219">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="a82d2-220">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="a82d2-220">A developer exception page</span></span>
* <span data-ttu-id="a82d2-221">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="a82d2-221">Custom error pages</span></span>
* <span data-ttu-id="a82d2-222">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="a82d2-222">Static status code pages</span></span>
* <span data-ttu-id="a82d2-223">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="a82d2-223">Startup exception handling</span></span>

<span data-ttu-id="a82d2-224">有关详细信息，请参阅 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-224">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="a82d2-225">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="a82d2-225">Make HTTP requests</span></span>

<span data-ttu-id="a82d2-226">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="a82d2-226">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="a82d2-227">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="a82d2-227">The factory:</span></span>

* <span data-ttu-id="a82d2-228">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="a82d2-228">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="a82d2-229">例如，可以注册 github  客户端，并将它配置为访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="a82d2-229">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="a82d2-230">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="a82d2-230">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="a82d2-231">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="a82d2-231">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="a82d2-232">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="a82d2-232">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="a82d2-233">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="a82d2-233">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="a82d2-234">与 Polly 集成，这是用于瞬时故障处理的常用第三方库  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-234">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="a82d2-235">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="a82d2-235">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="a82d2-236">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-236">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="a82d2-237">有关详细信息，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-237">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="a82d2-238">内容根</span><span class="sxs-lookup"><span data-stu-id="a82d2-238">Content root</span></span>

<span data-ttu-id="a82d2-239">内容根是指向以下内容的基本路径：</span><span class="sxs-lookup"><span data-stu-id="a82d2-239">The content root is the base path to the:</span></span>

* <span data-ttu-id="a82d2-240">托管应用程序的可执行文件 (.exe  )。</span><span class="sxs-lookup"><span data-stu-id="a82d2-240">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="a82d2-241">构成应用程序的已编译程序集 (.dll  )。</span><span class="sxs-lookup"><span data-stu-id="a82d2-241">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="a82d2-242">应用使用的非代码内容文件，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-242">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="a82d2-243">Razor 文件（.cshtml  、.razor  ）</span><span class="sxs-lookup"><span data-stu-id="a82d2-243">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="a82d2-244">配置文件（.json  、.xml  ）</span><span class="sxs-lookup"><span data-stu-id="a82d2-244">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="a82d2-245">数据文件 (.db  )</span><span class="sxs-lookup"><span data-stu-id="a82d2-245">Data files (*.db*)</span></span>
* <span data-ttu-id="a82d2-246">[Web 根目录](#web-root)，通常是已发布的 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="a82d2-246">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="a82d2-247">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="a82d2-247">During development:</span></span>

* <span data-ttu-id="a82d2-248">内容根默认为项目的根目录。</span><span class="sxs-lookup"><span data-stu-id="a82d2-248">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="a82d2-249">项目的根目录用于创建：</span><span class="sxs-lookup"><span data-stu-id="a82d2-249">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="a82d2-250">项目根目录中应用的非代码内容文件的路径。</span><span class="sxs-lookup"><span data-stu-id="a82d2-250">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="a82d2-251">[Web 根目录](#web-root)，通常是项目根目录中的 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="a82d2-251">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="a82d2-252">在[构建主机](#host)时，可以指定备用内容根路径。</span><span class="sxs-lookup"><span data-stu-id="a82d2-252">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="a82d2-253">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#contentrootpath>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-253">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

## <a name="web-root"></a><span data-ttu-id="a82d2-254">Web 根</span><span class="sxs-lookup"><span data-stu-id="a82d2-254">Web root</span></span>

<span data-ttu-id="a82d2-255">Web 根目录是公共、非代码、静态资源文件的基本路径，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-255">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="a82d2-256">样式表 (.css  )</span><span class="sxs-lookup"><span data-stu-id="a82d2-256">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="a82d2-257">JavaScript (.js  )</span><span class="sxs-lookup"><span data-stu-id="a82d2-257">JavaScript (*.js*)</span></span>
* <span data-ttu-id="a82d2-258">图像（.png  、.jpg  ）</span><span class="sxs-lookup"><span data-stu-id="a82d2-258">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="a82d2-259">默认情况下，静态文件仅从 Web 根目录（及子目录）提供。</span><span class="sxs-lookup"><span data-stu-id="a82d2-259">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="a82d2-260">Web 根路径默认为 {content root}/wwwroot  ，但[构建主机](#host)时可以指定其他 Web 根路径。</span><span class="sxs-lookup"><span data-stu-id="a82d2-260">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="a82d2-261">有关详细信息，请参阅 <xref:fundamentals/host/generic-host#webroot>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-261">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

<span data-ttu-id="a82d2-262">防止使用项目文件中的 [\<Content> 项目项](/visualstudio/msbuild/common-msbuild-project-items#content)在 wwwroot 中发布文件  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-262">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="a82d2-263">下面的示例会阻止在 wwwroot/local 目录和子目录中发布内容  ：</span><span class="sxs-lookup"><span data-stu-id="a82d2-263">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="a82d2-264">要阻止将静态标识资产发布到 Web 根目录，请参阅 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-264">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

<span data-ttu-id="a82d2-265">在 Razor (.cshtml  ) 文件中，波浪号斜杠 (`~/`) 指向 Web 根目录。</span><span class="sxs-lookup"><span data-stu-id="a82d2-265">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="a82d2-266">以 `~/` 开头的路径称为虚拟路径  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-266">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="a82d2-267">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-267">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a82d2-268">本文概述了了解如何开发 ASP.NET Core 应用的关键主题。</span><span class="sxs-lookup"><span data-stu-id="a82d2-268">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="a82d2-269">Startup 类</span><span class="sxs-lookup"><span data-stu-id="a82d2-269">The Startup class</span></span>

<span data-ttu-id="a82d2-270">`Startup` 类位于：</span><span class="sxs-lookup"><span data-stu-id="a82d2-270">The `Startup` class is where:</span></span>

* <span data-ttu-id="a82d2-271">已配置应用所需的服务。</span><span class="sxs-lookup"><span data-stu-id="a82d2-271">Services required by the app are configured.</span></span>
* <span data-ttu-id="a82d2-272">已定义请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="a82d2-272">The request handling pipeline is defined.</span></span>

<span data-ttu-id="a82d2-273">服务是应用使用的组件  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-273">*Services* are components that are used by the app.</span></span> <span data-ttu-id="a82d2-274">例如，日志记录组件就是一项服务。</span><span class="sxs-lookup"><span data-stu-id="a82d2-274">For example, a logging component is a service.</span></span> <span data-ttu-id="a82d2-275">将配置（或注册）服务的代码添加到 `Startup.ConfigureServices` 方法中  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-275">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="a82d2-276">请求处理管道由一系列中间件组件组成  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-276">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="a82d2-277">例如，中间件可能处理对静态文件的请求或将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a82d2-277">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="a82d2-278">每个中间件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-278">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="a82d2-279">将配置请求处理管道的代码添加到 `Startup.Configure` 方法中。</span><span class="sxs-lookup"><span data-stu-id="a82d2-279">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="a82d2-280">下面是 `Startup` 类示例：</span><span class="sxs-lookup"><span data-stu-id="a82d2-280">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="a82d2-281">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="a82d2-282">依赖关系注入（服务）</span><span class="sxs-lookup"><span data-stu-id="a82d2-282">Dependency injection (services)</span></span>

<span data-ttu-id="a82d2-283">ASP.NET Core 有内置的依赖关系注入 (DI) 框架，可使配置的服务适用于应用的类。</span><span class="sxs-lookup"><span data-stu-id="a82d2-283">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="a82d2-284">在类中获取服务实例的一种方法是使用所需类型的参数创建构造函数。</span><span class="sxs-lookup"><span data-stu-id="a82d2-284">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="a82d2-285">参数可以是服务类型或接口。</span><span class="sxs-lookup"><span data-stu-id="a82d2-285">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="a82d2-286">DI 系统在运行时提供服务。</span><span class="sxs-lookup"><span data-stu-id="a82d2-286">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="a82d2-287">下面是使用 DI 获取 Entity Framework Core 上下文对象的类。</span><span class="sxs-lookup"><span data-stu-id="a82d2-287">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="a82d2-288">突出显示的行是构造函数注入的示例：</span><span class="sxs-lookup"><span data-stu-id="a82d2-288">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="a82d2-289">虽然 DI 是内置的，但旨在允许插入第三方控制反转 (IoC) 容器（根据需要）。</span><span class="sxs-lookup"><span data-stu-id="a82d2-289">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="a82d2-290">有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-290">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="a82d2-291">中间件</span><span class="sxs-lookup"><span data-stu-id="a82d2-291">Middleware</span></span>

<span data-ttu-id="a82d2-292">请求处理管道由一系列中间件组件组成。</span><span class="sxs-lookup"><span data-stu-id="a82d2-292">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="a82d2-293">每个组件在 `HttpContext` 上执行异步操作，然后调用管道中的下一个中间件或终止请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-293">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="a82d2-294">按照惯例，通过在 `Startup.Configure` 方法中调用其 `Use...` 扩展方法，向管道添加中间件组件。</span><span class="sxs-lookup"><span data-stu-id="a82d2-294">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="a82d2-295">例如，要启用静态文件的呈现，请调用 `UseStaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="a82d2-295">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="a82d2-296">以下示例中突出显示的代码配置请求处理管道：</span><span class="sxs-lookup"><span data-stu-id="a82d2-296">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="a82d2-297">ASP.NET Core 包含一组丰富的内置中间件，并且你也可以编写自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="a82d2-297">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="a82d2-298">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-298">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="a82d2-299">主机</span><span class="sxs-lookup"><span data-stu-id="a82d2-299">Host</span></span>

<span data-ttu-id="a82d2-300">ASP.NET Core 应用在启动时构建主机  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-300">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="a82d2-301">主机是封装所有应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-301">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="a82d2-302">HTTP 服务器实现</span><span class="sxs-lookup"><span data-stu-id="a82d2-302">An HTTP server implementation</span></span>
* <span data-ttu-id="a82d2-303">中间件组件</span><span class="sxs-lookup"><span data-stu-id="a82d2-303">Middleware components</span></span>
* <span data-ttu-id="a82d2-304">Logging</span><span class="sxs-lookup"><span data-stu-id="a82d2-304">Logging</span></span>
* <span data-ttu-id="a82d2-305">DI</span><span class="sxs-lookup"><span data-stu-id="a82d2-305">DI</span></span>
* <span data-ttu-id="a82d2-306">Configuration</span><span class="sxs-lookup"><span data-stu-id="a82d2-306">Configuration</span></span>

<span data-ttu-id="a82d2-307">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="a82d2-307">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="a82d2-308">提供两个主机：Web 主机和通用主机。</span><span class="sxs-lookup"><span data-stu-id="a82d2-308">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="a82d2-309">在 ASP.NET Core 2.x 上，通用主机仅用于非 Web 方案。</span><span class="sxs-lookup"><span data-stu-id="a82d2-309">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="a82d2-310">要创建主机的代码位于 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="a82d2-310">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="a82d2-311">`CreateDefaultBuilder` 方法配置具有常用选项的主机，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a82d2-311">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="a82d2-312">将 [Kestrel](#servers) 用作 Web 服务器并启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="a82d2-312">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="a82d2-313">从 appsettings.json、appsettings.[EnvironmentName].json、环境变量、命令行参数和其他配置源中加载配置   。</span><span class="sxs-lookup"><span data-stu-id="a82d2-313">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="a82d2-314">将日志记录输出发送到控制台并调试提供程序。</span><span class="sxs-lookup"><span data-stu-id="a82d2-314">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="a82d2-315">有关详细信息，请参阅 <xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-315">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="a82d2-316">非 Web 方案</span><span class="sxs-lookup"><span data-stu-id="a82d2-316">Non-web scenarios</span></span>

<span data-ttu-id="a82d2-317">其他类型的应用可通过通用主机使用横切框架扩展，例如日志记录、依赖项注入 (DI)、配置和应用生命周期管理。</span><span class="sxs-lookup"><span data-stu-id="a82d2-317">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="a82d2-318">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-318">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="a82d2-319">服务器</span><span class="sxs-lookup"><span data-stu-id="a82d2-319">Servers</span></span>

<span data-ttu-id="a82d2-320">ASP.NET Core 应用使用 HTTP 服务器实现侦听 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-320">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="a82d2-321">服务器对应用的请求在表面上呈现为一组由 `HttpContext` 组成的[请求功能](xref:fundamentals/request-features)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-321">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="a82d2-322">Windows</span><span class="sxs-lookup"><span data-stu-id="a82d2-322">Windows</span></span>](#tab/windows)

<span data-ttu-id="a82d2-323">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="a82d2-323">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="a82d2-324">Kestrel 是跨平台 Web 服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-324">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="a82d2-325">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-325">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="a82d2-326">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-326">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="a82d2-327">IIS HTTP 服务器是适用于使用 IIS 的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-327">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="a82d2-328">借助此服务器，ASP.NET Core 应用和 IIS 在同一进程中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-328">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="a82d2-329">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-329">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="a82d2-330">macOS</span><span class="sxs-lookup"><span data-stu-id="a82d2-330">macOS</span></span>](#tab/macos)

<span data-ttu-id="a82d2-331">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-331">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a82d2-332">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-332">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a82d2-333">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-333">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="a82d2-334">Linux</span><span class="sxs-lookup"><span data-stu-id="a82d2-334">Linux</span></span>](#tab/linux)

<span data-ttu-id="a82d2-335">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-335">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a82d2-336">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-336">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a82d2-337">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-337">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="a82d2-338">Windows</span><span class="sxs-lookup"><span data-stu-id="a82d2-338">Windows</span></span>](#tab/windows)

<span data-ttu-id="a82d2-339">ASP.NET Core 提供以下服务器实现：</span><span class="sxs-lookup"><span data-stu-id="a82d2-339">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="a82d2-340">Kestrel 是跨平台 Web 服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-340">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="a82d2-341">Kestrel 通常使用 [IIS](https://www.iis.net/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-341">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="a82d2-342">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-342">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="a82d2-343">HTTP.sys是适用于不与 IIS 一起使用的 Windows 的服务器  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-343">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="a82d2-344">macOS</span><span class="sxs-lookup"><span data-stu-id="a82d2-344">macOS</span></span>](#tab/macos)

<span data-ttu-id="a82d2-345">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-345">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a82d2-346">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-346">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a82d2-347">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-347">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="a82d2-348">Linux</span><span class="sxs-lookup"><span data-stu-id="a82d2-348">Linux</span></span>](#tab/linux)

<span data-ttu-id="a82d2-349">ASP.NET Core 提供 Kestrel 跨平台服务器实现  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-349">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="a82d2-350">Kestrel 可作为面向公众的边缘服务器运行，直接向 Internet 公开。</span><span class="sxs-lookup"><span data-stu-id="a82d2-350">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="a82d2-351">Kestrel 通常使用 [Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/) 在反向代理配置中运行。</span><span class="sxs-lookup"><span data-stu-id="a82d2-351">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a82d2-352">有关详细信息，请参阅 <xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-352">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="a82d2-353">Configuration</span><span class="sxs-lookup"><span data-stu-id="a82d2-353">Configuration</span></span>

<span data-ttu-id="a82d2-354">ASP.NET Core 提供了配置框架，可以从配置提供程序的有序集中将设置作为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="a82d2-354">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="a82d2-355">有适用于各种源的内置配置提供程序，例如 .json 文件、.xml 文件、环境变量和命令行参数   。</span><span class="sxs-lookup"><span data-stu-id="a82d2-355">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="a82d2-356">此外可以编写自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="a82d2-356">You can also write custom configuration providers.</span></span>

<span data-ttu-id="a82d2-357">例如，可以指定配置来自 appsettings.json 和环境变量  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-357">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="a82d2-358">然后，当请求 ConnectionString 的值时，框架首先在 appsettings.json 文件中查找   。</span><span class="sxs-lookup"><span data-stu-id="a82d2-358">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="a82d2-359">如果也在环境变量中找到了值，那么来自环境变量的值将优先使用。</span><span class="sxs-lookup"><span data-stu-id="a82d2-359">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="a82d2-360">为了管理密码等机密配置数据，ASP.NET Core 提供了[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-360">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a82d2-361">对于生产机密，建议使用 [Azure 密钥保管库](xref:security/key-vault-configuration)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-361">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="a82d2-362">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-362">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="a82d2-363">选项</span><span class="sxs-lookup"><span data-stu-id="a82d2-363">Options</span></span>

<span data-ttu-id="a82d2-364">在可能的情况下，ASP.NET Core 按照选项模式来存储和检索配置值  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-364">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="a82d2-365">选项模式使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="a82d2-365">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="a82d2-366">例如，以下代码可以设置 WebSocket 选项：</span><span class="sxs-lookup"><span data-stu-id="a82d2-366">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="a82d2-367">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-367">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="a82d2-368">环境</span><span class="sxs-lookup"><span data-stu-id="a82d2-368">Environments</span></span>

<span data-ttu-id="a82d2-369">执行环境（例如“开发”、“暂存”和“生产”）是 ASP.NET Core 中的高级概念    。</span><span class="sxs-lookup"><span data-stu-id="a82d2-369">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="a82d2-370">可以通过设置 `ASPNETCORE_ENVIRONMENT` 环境变量来指定运行应用的环境。</span><span class="sxs-lookup"><span data-stu-id="a82d2-370">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="a82d2-371">ASP.NET Core 在应用启动时读取该环境变量，并将该值存储在 `IHostingEnvironment` 实现中。</span><span class="sxs-lookup"><span data-stu-id="a82d2-371">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="a82d2-372">可通过 DI 在应用的任何位置使用环境对象。</span><span class="sxs-lookup"><span data-stu-id="a82d2-372">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="a82d2-373">以下来自 `Startup` 类的示例代码将应用配置为仅在开发过程中运行时提供详细的错误信息：</span><span class="sxs-lookup"><span data-stu-id="a82d2-373">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="a82d2-374">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-374">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="a82d2-375">Logging</span><span class="sxs-lookup"><span data-stu-id="a82d2-375">Logging</span></span>

<span data-ttu-id="a82d2-376">ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。</span><span class="sxs-lookup"><span data-stu-id="a82d2-376">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="a82d2-377">可用的提供程序包括：</span><span class="sxs-lookup"><span data-stu-id="a82d2-377">Available providers include the following:</span></span>

* <span data-ttu-id="a82d2-378">控制台</span><span class="sxs-lookup"><span data-stu-id="a82d2-378">Console</span></span>
* <span data-ttu-id="a82d2-379">调试</span><span class="sxs-lookup"><span data-stu-id="a82d2-379">Debug</span></span>
* <span data-ttu-id="a82d2-380">Windows 事件跟踪</span><span class="sxs-lookup"><span data-stu-id="a82d2-380">Event Tracing on Windows</span></span>
* <span data-ttu-id="a82d2-381">Windows 事件日志</span><span class="sxs-lookup"><span data-stu-id="a82d2-381">Windows Event Log</span></span>
* <span data-ttu-id="a82d2-382">TraceSource</span><span class="sxs-lookup"><span data-stu-id="a82d2-382">TraceSource</span></span>
* <span data-ttu-id="a82d2-383">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="a82d2-383">Azure App Service</span></span>
* <span data-ttu-id="a82d2-384">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="a82d2-384">Azure Application Insights</span></span>

<span data-ttu-id="a82d2-385">通过从 DI 获取 `ILogger` 对象并调用日志方法，从应用代码中的任何位置写入日志。</span><span class="sxs-lookup"><span data-stu-id="a82d2-385">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="a82d2-386">下面是使用 `ILogger` 对象的示例代码，其中突出显示了构造函数注入和日志记录方法调用。</span><span class="sxs-lookup"><span data-stu-id="a82d2-386">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="a82d2-387">通过 `ILogger` 接口，可将任意数量的字段传递给日志提供程序。</span><span class="sxs-lookup"><span data-stu-id="a82d2-387">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="a82d2-388">字段通常用于构造消息字符串，但提供程序还可将其作为单独的字段发送到数据存储。</span><span class="sxs-lookup"><span data-stu-id="a82d2-388">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="a82d2-389">此功能使日志提供程序可以实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-389">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="a82d2-390">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-390">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="a82d2-391">路由</span><span class="sxs-lookup"><span data-stu-id="a82d2-391">Routing</span></span>

<span data-ttu-id="a82d2-392">路由是映射到处理程序的 URL 模式  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-392">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="a82d2-393">处理程序通常是 Razor 页面、MVC 控制器中的操作方法或中间件。</span><span class="sxs-lookup"><span data-stu-id="a82d2-393">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="a82d2-394">借助 ASP.NET Core 路由，可以控制应用使用的 URL。</span><span class="sxs-lookup"><span data-stu-id="a82d2-394">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="a82d2-395">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-395">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="a82d2-396">错误处理</span><span class="sxs-lookup"><span data-stu-id="a82d2-396">Error handling</span></span>

<span data-ttu-id="a82d2-397">ASP.NET Core 具有用于处理错误的内置功能，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-397">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="a82d2-398">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="a82d2-398">A developer exception page</span></span>
* <span data-ttu-id="a82d2-399">自定义错误页</span><span class="sxs-lookup"><span data-stu-id="a82d2-399">Custom error pages</span></span>
* <span data-ttu-id="a82d2-400">静态状态代码页</span><span class="sxs-lookup"><span data-stu-id="a82d2-400">Static status code pages</span></span>
* <span data-ttu-id="a82d2-401">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="a82d2-401">Startup exception handling</span></span>

<span data-ttu-id="a82d2-402">有关详细信息，请参阅 <xref:fundamentals/error-handling>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-402">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="a82d2-403">发出 HTTP 请求</span><span class="sxs-lookup"><span data-stu-id="a82d2-403">Make HTTP requests</span></span>

<span data-ttu-id="a82d2-404">`IHttpClientFactory` 的实现可用于创建 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="a82d2-404">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="a82d2-405">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="a82d2-405">The factory:</span></span>

* <span data-ttu-id="a82d2-406">提供一个中心位置，用于命名和配置逻辑 `HttpClient` 实例。</span><span class="sxs-lookup"><span data-stu-id="a82d2-406">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="a82d2-407">例如，可以注册 github  客户端，并将它配置为访问 GitHub。</span><span class="sxs-lookup"><span data-stu-id="a82d2-407">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="a82d2-408">可以注册一个默认客户端用于其他用途。</span><span class="sxs-lookup"><span data-stu-id="a82d2-408">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="a82d2-409">支持多个委托处理程序的注册和链接，以生成出站请求中间件管道。</span><span class="sxs-lookup"><span data-stu-id="a82d2-409">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="a82d2-410">此模式类似于 ASP.NET Core 中的入站中间件管道。</span><span class="sxs-lookup"><span data-stu-id="a82d2-410">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="a82d2-411">此模式提供了一种用于管理围绕 HTTP 请求的横切关注点的机制，包括缓存、错误处理、序列化以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="a82d2-411">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="a82d2-412">与 Polly 集成，这是用于瞬时故障处理的常用第三方库  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-412">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="a82d2-413">管理基础 `HttpClientMessageHandler` 实例的池和生存期，避免在手动管理 `HttpClient` 生存期时出现常见的 DNS 问题。</span><span class="sxs-lookup"><span data-stu-id="a82d2-413">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="a82d2-414">（通过 `ILogger`）添加可配置的记录体验，以处理工厂创建的客户端发送的所有请求。</span><span class="sxs-lookup"><span data-stu-id="a82d2-414">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="a82d2-415">有关详细信息，请参阅 <xref:fundamentals/http-requests>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-415">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="a82d2-416">内容根</span><span class="sxs-lookup"><span data-stu-id="a82d2-416">Content root</span></span>

<span data-ttu-id="a82d2-417">内容根是指向以下内容的基本路径：</span><span class="sxs-lookup"><span data-stu-id="a82d2-417">The content root is the base path to the:</span></span>

* <span data-ttu-id="a82d2-418">托管应用程序的可执行文件 (.exe  )。</span><span class="sxs-lookup"><span data-stu-id="a82d2-418">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="a82d2-419">构成应用程序的已编译程序集 (.dll  )。</span><span class="sxs-lookup"><span data-stu-id="a82d2-419">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="a82d2-420">应用使用的非代码内容文件，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-420">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="a82d2-421">Razor 文件（.cshtml  、.razor  ）</span><span class="sxs-lookup"><span data-stu-id="a82d2-421">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="a82d2-422">配置文件（.json  、.xml  ）</span><span class="sxs-lookup"><span data-stu-id="a82d2-422">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="a82d2-423">数据文件 (.db  )</span><span class="sxs-lookup"><span data-stu-id="a82d2-423">Data files (*.db*)</span></span>
* <span data-ttu-id="a82d2-424">[Web 根目录](#web-root)，通常是已发布的 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="a82d2-424">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="a82d2-425">在开发过程中：</span><span class="sxs-lookup"><span data-stu-id="a82d2-425">During development:</span></span>

* <span data-ttu-id="a82d2-426">内容根默认为项目的根目录。</span><span class="sxs-lookup"><span data-stu-id="a82d2-426">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="a82d2-427">项目的根目录用于创建：</span><span class="sxs-lookup"><span data-stu-id="a82d2-427">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="a82d2-428">项目根目录中应用的非代码内容文件的路径。</span><span class="sxs-lookup"><span data-stu-id="a82d2-428">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="a82d2-429">[Web 根目录](#web-root)，通常是项目根目录中的 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="a82d2-429">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="a82d2-430">在[构建主机](#host)时，可以指定备用内容根路径。</span><span class="sxs-lookup"><span data-stu-id="a82d2-430">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="a82d2-431">有关详细信息，请参阅 <xref:fundamentals/host/web-host#content-root>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-431">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="a82d2-432">Web 根</span><span class="sxs-lookup"><span data-stu-id="a82d2-432">Web root</span></span>

<span data-ttu-id="a82d2-433">Web 根目录是公共、非代码、静态资源文件的基本路径，例如：</span><span class="sxs-lookup"><span data-stu-id="a82d2-433">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="a82d2-434">样式表 (.css  )</span><span class="sxs-lookup"><span data-stu-id="a82d2-434">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="a82d2-435">JavaScript (.js  )</span><span class="sxs-lookup"><span data-stu-id="a82d2-435">JavaScript (*.js*)</span></span>
* <span data-ttu-id="a82d2-436">图像（.png  、.jpg  ）</span><span class="sxs-lookup"><span data-stu-id="a82d2-436">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="a82d2-437">默认情况下，静态文件仅从 Web 根目录（及子目录）提供。</span><span class="sxs-lookup"><span data-stu-id="a82d2-437">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="a82d2-438">Web 根路径默认为 {content root}/wwwroot  ，但[构建主机](#host)时可以指定其他 Web 根路径。</span><span class="sxs-lookup"><span data-stu-id="a82d2-438">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="a82d2-439">有关详细信息，请参阅 [Web 根目录](xref:fundamentals/host/web-host#web-root)。</span><span class="sxs-lookup"><span data-stu-id="a82d2-439">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="a82d2-440">防止使用项目文件中的 [\<Content> 项目项](/visualstudio/msbuild/common-msbuild-project-items#content)在 wwwroot 中发布文件  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-440">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="a82d2-441">下面的示例会阻止在 wwwroot/local 目录和子目录中发布内容  ：</span><span class="sxs-lookup"><span data-stu-id="a82d2-441">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="a82d2-442">在 Razor (.cshtml  ) 文件中，波浪号斜杠 (`~/`) 指向 Web 根目录。</span><span class="sxs-lookup"><span data-stu-id="a82d2-442">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="a82d2-443">以 `~/` 开头的路径称为虚拟路径  。</span><span class="sxs-lookup"><span data-stu-id="a82d2-443">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="a82d2-444">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="a82d2-444">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end