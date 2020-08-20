---
title: 从 ASP.NET 迁移到 ASP.NET Core
author: isaac2004
description: 接收将现有 ASP.NET MVC 或 Web API 应用迁移到 ASP.NET Core Web 的指南
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7f5d2835d93631ac73b3da0c3dc26d87ef64c57d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634756"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="0c088-103">从 ASP.NET 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c088-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="0c088-104">作者：[Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="0c088-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="0c088-105">本文可作为从 ASP.NET 应用迁移到 ASP.NET Core 的参考指南。</span><span class="sxs-lookup"><span data-stu-id="0c088-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c088-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="0c088-106">Prerequisites</span></span>

[<span data-ttu-id="0c088-107">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0c088-107">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a><span data-ttu-id="0c088-108">目标框架</span><span class="sxs-lookup"><span data-stu-id="0c088-108">Target frameworks</span></span>

<span data-ttu-id="0c088-109">ASP.NET Core 项目为开发人员提供了面向 .NET Core 和/或 .NET Framework 的灵活性。</span><span class="sxs-lookup"><span data-stu-id="0c088-109">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="0c088-110">若要确定最合适的目标框架，请参阅[为服务器应用选择 .NET Core 或 .NET Framework](/dotnet/standard/choosing-core-framework-server)。</span><span class="sxs-lookup"><span data-stu-id="0c088-110">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="0c088-111">面向 .NET Framework 时，项目需要引用单个 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="0c088-111">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="0c088-112">得益于有 ASP.NET Core [元包](xref:fundamentals/metapackage-app)，面向 .NET Core 时可以避免进行大量的显式包引用。</span><span class="sxs-lookup"><span data-stu-id="0c088-112">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="0c088-113">在项目中安装 `Microsoft.AspNetCore.App` 元包：</span><span class="sxs-lookup"><span data-stu-id="0c088-113">Install the `Microsoft.AspNetCore.App` metapackage in your project:</span></span>

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

<span data-ttu-id="0c088-114">使用此元包时，应用不会部署元包中引用的任何包。</span><span class="sxs-lookup"><span data-stu-id="0c088-114">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="0c088-115">.NET Core 运行时存储中包含这些资产，并已预编译，旨在提升性能。</span><span class="sxs-lookup"><span data-stu-id="0c088-115">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="0c088-116">如需了解更多详情，请参阅[用于 ASP.NET Core 的 Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="0c088-116">See [Microsoft.AspNetCore.App metapackage for ASP.NET Core](xref:fundamentals/metapackage-app) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="0c088-117">项目结构差异</span><span class="sxs-lookup"><span data-stu-id="0c088-117">Project structure differences</span></span>

<span data-ttu-id="0c088-118">ASP.NET Core 中简化了 .csproj 文件格式。</span><span class="sxs-lookup"><span data-stu-id="0c088-118">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="0c088-119">下面是一些显著的更改：</span><span class="sxs-lookup"><span data-stu-id="0c088-119">Some notable changes include:</span></span>

- <span data-ttu-id="0c088-120">无需显式添加，即可将文件视作项目的一部分。</span><span class="sxs-lookup"><span data-stu-id="0c088-120">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="0c088-121">服务于大型团队时，这可减少出现 XML 合并冲突的风险。</span><span class="sxs-lookup"><span data-stu-id="0c088-121">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="0c088-122">没有对其他项目的基于 GUID 的引用，这可以提高文件的可读性。</span><span class="sxs-lookup"><span data-stu-id="0c088-122">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="0c088-123">无需在 Visual Studio 中卸载文件即可对它进行编辑：</span><span class="sxs-lookup"><span data-stu-id="0c088-123">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Visual Studio 2017 中的“编辑 CSPROJ”上下文菜单选项](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="0c088-125">Global.asax 文件替换</span><span class="sxs-lookup"><span data-stu-id="0c088-125">Global.asax file replacement</span></span>

<span data-ttu-id="0c088-126">ASP.NET Core 引入了启动应用的新机制。</span><span class="sxs-lookup"><span data-stu-id="0c088-126">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="0c088-127">ASP.NET 应用程序的入口点是 Global.asax 文件。</span><span class="sxs-lookup"><span data-stu-id="0c088-127">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="0c088-128">路由配置及筛选器和区域注册等任务在 Global.asax 文件中进行处理。</span><span class="sxs-lookup"><span data-stu-id="0c088-128">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="0c088-129">此方法会将应用程序和应用程序要部署到的服务器耦合在一起，并且它们的耦合方式会干扰实现。</span><span class="sxs-lookup"><span data-stu-id="0c088-129">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="0c088-130">为了将它们分离，引入了 [OWIN](https://owin.org/) 来提供一种更为简便的同时使用多个框架的方法。</span><span class="sxs-lookup"><span data-stu-id="0c088-130">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="0c088-131">OWIN 提供了一个管道，可以只添加所需的模块。</span><span class="sxs-lookup"><span data-stu-id="0c088-131">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="0c088-132">托管环境使用 [Startup](xref:fundamentals/startup) 函数配置服务和应用的请求管道。</span><span class="sxs-lookup"><span data-stu-id="0c088-132">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="0c088-133">`Startup` 在应用程序中注册一组中间件。</span><span class="sxs-lookup"><span data-stu-id="0c088-133">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="0c088-134">对于每个请求，应用程序都使用现有处理程序集的链接列表的头指针调用各个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="0c088-134">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="0c088-135">每个中间件组件可以向请求处理管道添加一个或多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="0c088-135">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="0c088-136">为此，需要返回对成为列表新头的处理程序的引用。</span><span class="sxs-lookup"><span data-stu-id="0c088-136">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="0c088-137">每个处理程序负责记住并调用列表中的下一个处理程序。</span><span class="sxs-lookup"><span data-stu-id="0c088-137">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="0c088-138">使用 ASP.NET Core 时，应用程序的入口点是 `Startup`，不再具有 Global.asax 的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="0c088-138">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="0c088-139">结合使用 OWIN 和 .NET Framework 时，使用的管道应如下所示：</span><span class="sxs-lookup"><span data-stu-id="0c088-139">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="0c088-140">这会配置默认路由，默认为 XmlSerialization 而不是 Json。</span><span class="sxs-lookup"><span data-stu-id="0c088-140">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="0c088-141">根据需要向此管道添加其他中间件（加载服务、配置设置、静态文件等）。</span><span class="sxs-lookup"><span data-stu-id="0c088-141">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="0c088-142">ASP.NET Core 使用相似的方法，但是不依赖 OWIN 处理条目。</span><span class="sxs-lookup"><span data-stu-id="0c088-142">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="0c088-143">而是通过 Program.cs `Main` 方法（类似于控制台应用程序）来完成，并且 `Startup` 会通过该处进行加载。</span><span class="sxs-lookup"><span data-stu-id="0c088-143">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="0c088-144">`Startup` 必须包含 `Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="0c088-144">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="0c088-145">在 `Configure` 中，向管道添加必要的中间件。</span><span class="sxs-lookup"><span data-stu-id="0c088-145">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="0c088-146">在下面的示例（来自默认网站模板）中，扩展方法为管道配置以下支持：</span><span class="sxs-lookup"><span data-stu-id="0c088-146">In the following example (from the default web site template), extension methods configure the pipeline with support for:</span></span>

- <span data-ttu-id="0c088-147">错误页</span><span class="sxs-lookup"><span data-stu-id="0c088-147">Error pages</span></span>
- <span data-ttu-id="0c088-148">HTTP 严格传输安全</span><span class="sxs-lookup"><span data-stu-id="0c088-148">HTTP Strict Transport Security</span></span>
- <span data-ttu-id="0c088-149">从 HTTP 重定向到 HTTPS</span><span class="sxs-lookup"><span data-stu-id="0c088-149">HTTP redirection to HTTPS</span></span>
- <span data-ttu-id="0c088-150">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0c088-150">ASP.NET Core MVC</span></span>

[!code-csharp[](samples/startup.cs)]

<span data-ttu-id="0c088-151">现在主机和应用程序已分离，这样将来就可以灵活地迁移到其他平台。</span><span class="sxs-lookup"><span data-stu-id="0c088-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="0c088-152">若要获取 ASP.NET Core Startup 和中间件的更深入的参考信息，请参阅 [ASP.NET Core 中的 Startup](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="0c088-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="0c088-153">存储配置</span><span class="sxs-lookup"><span data-stu-id="0c088-153">Store configurations</span></span>

<span data-ttu-id="0c088-154">ASP.NET 支持存储设置。</span><span class="sxs-lookup"><span data-stu-id="0c088-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="0c088-155">这些设置可用于支持应用程序已部署到的环境（以此用途为例）。</span><span class="sxs-lookup"><span data-stu-id="0c088-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="0c088-156">常见做法是将所有的自定义键值对存储在 Web.config 文件的 `<appSettings>` 部分中：</span><span class="sxs-lookup"><span data-stu-id="0c088-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="0c088-157">应用程序使用 `System.Configuration` 命名空间中的 `ConfigurationManager.AppSettings` 集合读取这些设置：</span><span class="sxs-lookup"><span data-stu-id="0c088-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="0c088-158">ASP.NET Core 可以将应用程序的配置数据存储在任何文件中，并可在启动中间件的过程中加载它们。</span><span class="sxs-lookup"><span data-stu-id="0c088-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="0c088-159">项目模板中使用的默认文件是 appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="0c088-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="0c088-160">将此文件加载到应用程序内的 `IConfiguration` 的实例的过程在 Startup.cs 中完成：</span><span class="sxs-lookup"><span data-stu-id="0c088-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="0c088-161">应用读取 `Configuration` 来获得这些设置：</span><span class="sxs-lookup"><span data-stu-id="0c088-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="0c088-162">此方法有扩展项，它们可使此过程更加可靠，例如使用[依存关系注入](xref:fundamentals/dependency-injection) (DI) 来加载使用这些值的服务。</span><span class="sxs-lookup"><span data-stu-id="0c088-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="0c088-163">DI 方法提供了一组强类型的配置对象。</span><span class="sxs-lookup"><span data-stu-id="0c088-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> <span data-ttu-id="0c088-164">若要获取 ASP.NET Core 配置的更深入的参考信息，请参阅 [ASP.NET Core 中的配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="0c088-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="0c088-165">本机依存关系注入</span><span class="sxs-lookup"><span data-stu-id="0c088-165">Native dependency injection</span></span>

<span data-ttu-id="0c088-166">生成大型可缩放应用程序时，一个重要的目标是将组件和服务松散耦合。</span><span class="sxs-lookup"><span data-stu-id="0c088-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="0c088-167">[依赖项注入](xref:fundamentals/dependency-injection)不仅是可实现此目标的常用技术，还是 ASP.NET Core 的本机组件。</span><span class="sxs-lookup"><span data-stu-id="0c088-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="0c088-168">在 ASP.NET 应用中，开发人员依赖第三方库实现依存关系注入。</span><span class="sxs-lookup"><span data-stu-id="0c088-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="0c088-169">其中的一个库是 Microsoft 模式和做法提供的 [Unity](https://github.com/unitycontainer/unity)。</span><span class="sxs-lookup"><span data-stu-id="0c088-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="0c088-170">实现打包 `UnityContainer` 的 `IDependencyResolver` 是使用 Unity 设置依存关系注入的一个示例：</span><span class="sxs-lookup"><span data-stu-id="0c088-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="0c088-171">创建 `UnityContainer` 的实例，注册服务，然后将 `HttpConfiguration` 的依赖关系解析程序设置为容器的 `UnityResolver` 新实例：</span><span class="sxs-lookup"><span data-stu-id="0c088-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="0c088-172">在必要时注入 `IProductRepository`：</span><span class="sxs-lookup"><span data-stu-id="0c088-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="0c088-173">由于依存关系注入是 ASP.NET Core 的组成部分，因此可以在 Startup.cs 的 `ConfigureServices` 方法中添加你的服务：</span><span class="sxs-lookup"><span data-stu-id="0c088-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="0c088-174">可在任意位置注入存储库，Unity 亦是如此。</span><span class="sxs-lookup"><span data-stu-id="0c088-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="0c088-175">有关依赖关系注入的详细信息，请参阅[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="0c088-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="0c088-176">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="0c088-176">Serve static files</span></span>

<span data-ttu-id="0c088-177">Web 开发的一个重要环节是提供客户端静态资产的功能。</span><span class="sxs-lookup"><span data-stu-id="0c088-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="0c088-178">HTML、CSS、Javascript 和图像是最常见的静态文件示例。</span><span class="sxs-lookup"><span data-stu-id="0c088-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="0c088-179">这些文件需要保存在应用（或 CDN）的发布位置中，并且需要引用它们，以便请求可以加载这些文件。</span><span class="sxs-lookup"><span data-stu-id="0c088-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="0c088-180">在 ASP.NET Core 中，此过程发生了变化。</span><span class="sxs-lookup"><span data-stu-id="0c088-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="0c088-181">在 ASP.NET 中，静态文件存储在各种目录中，并在视图中进行引用。</span><span class="sxs-lookup"><span data-stu-id="0c088-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="0c088-182">在 ASP.NET Core 中，静态文件存储在“Web 根”（&lt;内容根&gt;/wwwroot）中，除非另有配置。</span><span class="sxs-lookup"><span data-stu-id="0c088-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="0c088-183">通过从 `Startup.Configure` 调用 `UseStaticFiles` 扩展方法将这些文件加载到请求管道中：</span><span class="sxs-lookup"><span data-stu-id="0c088-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="0c088-184">如果面向 .NET Framework，请安装 NuGet 包 `Microsoft.AspNetCore.StaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="0c088-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="0c088-185">例如，可以通过浏览器从类似 `http://<app>/images/<imageFileName>` 的位置访问 wwwroot/images 文件夹中的图像资产。</span><span class="sxs-lookup"><span data-stu-id="0c088-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="0c088-186">若要获取在 ASP.NET Core 中提供静态文件的更深入的参考信息，请参阅[静态文件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="0c088-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="multi-value-no-loccookies"></a><span data-ttu-id="0c088-187">多值 cookie</span><span class="sxs-lookup"><span data-stu-id="0c088-187">Multi-value cookies</span></span>

<span data-ttu-id="0c088-188">ASP.NET Core 不支持[多值 cookie](xref:System.Web.HttpCookie.Values)。</span><span class="sxs-lookup"><span data-stu-id="0c088-188">[Multi-value cookies](xref:System.Web.HttpCookie.Values) aren't supported in ASP.NET Core.</span></span> <span data-ttu-id="0c088-189">为每个值创建一个 cookie。</span><span class="sxs-lookup"><span data-stu-id="0c088-189">Create one cookie per value.</span></span>

## <a name="partial-app-migration"></a><span data-ttu-id="0c088-190">部分应用迁移</span><span class="sxs-lookup"><span data-stu-id="0c088-190">Partial app migration</span></span>

<span data-ttu-id="0c088-191">部分应用迁移的一种方法是创建 IIS 子应用程序，只将特定的路由从 ASP.NET 4.x 迁移到 ASP.NET Core，同时保留应用的 URL 结构。</span><span class="sxs-lookup"><span data-stu-id="0c088-191">One approach to partial app migration is to create an IIS sub-application and only move certain routes from ASP.NET 4.x to ASP.NET Core while preserving the URL structure the app.</span></span> <span data-ttu-id="0c088-192">例如，applicationHost.config 文件中应用的 URL 结构：</span><span class="sxs-lookup"><span data-stu-id="0c088-192">For example, consider the URL structure of the app from the *applicationHost.config* file:</span></span>

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

<span data-ttu-id="0c088-193">目录结构：</span><span class="sxs-lookup"><span data-stu-id="0c088-193">Directory structure:</span></span>

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="additional-resources"></a><span data-ttu-id="0c088-194">其他资源</span><span class="sxs-lookup"><span data-stu-id="0c088-194">Additional resources</span></span>

- [<span data-ttu-id="0c088-195">将库移植到 .NET Core</span><span class="sxs-lookup"><span data-stu-id="0c088-195">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
