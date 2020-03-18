---
title: 从 ASP.NET 迁移到 ASP.NET Core 2.0
author: isaac2004
description: 接收有关现有 ASP.NET MVC 或 Web API 应用程序迁移到 ASP.NET Core 2.0 的指导。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
uid: migration/mvc2
ms.openlocfilehash: 5a2437f8dddee49d402bf7df67cb15aa895367b2
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511076"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a><span data-ttu-id="be898-103">从 ASP.NET 迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="be898-103">Migrate from ASP.NET to ASP.NET Core 2.0</span></span>

<span data-ttu-id="be898-104">作者：[Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="be898-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="be898-105">本文可作为从 ASP.NET 应用程序迁移到 ASP.NET Core 2.0 的参考指南。</span><span class="sxs-lookup"><span data-stu-id="be898-105">This article serves as a reference guide for migrating ASP.NET applications to ASP.NET Core 2.0.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="be898-106">先决条件</span><span class="sxs-lookup"><span data-stu-id="be898-106">Prerequisites</span></span>

<span data-ttu-id="be898-107">从 .Net 下载安装以下内容**之一** [： Windows](https://dotnet.microsoft.com/download)：</span><span class="sxs-lookup"><span data-stu-id="be898-107">Install **one** of the following from [.NET Downloads: Windows](https://dotnet.microsoft.com/download):</span></span>

* <span data-ttu-id="be898-108">.NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="be898-108">.NET Core SDK</span></span>
* <span data-ttu-id="be898-109">适用于 Windows 的 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be898-109">Visual Studio for Windows</span></span>
  * <span data-ttu-id="be898-110">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="be898-110">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="be898-111">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="be898-111">**.NET Core cross-platform development** workload</span></span>

## <a name="target-frameworks"></a><span data-ttu-id="be898-112">目标框架</span><span class="sxs-lookup"><span data-stu-id="be898-112">Target frameworks</span></span>

<span data-ttu-id="be898-113">ASP.NET Core 2.0 项目为开发人员提供了面向 .NET Core、.NET Framework 或同时面向这两者的灵活性。</span><span class="sxs-lookup"><span data-stu-id="be898-113">ASP.NET Core 2.0 projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="be898-114">若要确定最合适的目标框架，请参阅[为服务器应用选择 .NET Core 或 .NET Framework](/dotnet/standard/choosing-core-framework-server)。</span><span class="sxs-lookup"><span data-stu-id="be898-114">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="be898-115">面向 .NET Framework 时，项目需要引用单个 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="be898-115">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="be898-116">得益于有 ASP.NET Core 2.0 [元包](xref:fundamentals/metapackage)，面向 .NET Core 时可以避免进行大量的显式包引用。</span><span class="sxs-lookup"><span data-stu-id="be898-116">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core 2.0 [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="be898-117">在项目中安装 `Microsoft.AspNetCore.All` 元包：</span><span class="sxs-lookup"><span data-stu-id="be898-117">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

<span data-ttu-id="be898-118">使用此元包时，应用不会部署元包中引用的任何包。</span><span class="sxs-lookup"><span data-stu-id="be898-118">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="be898-119">.NET Core 运行时存储中包含这些资产，并已预编译，旨在提升性能。</span><span class="sxs-lookup"><span data-stu-id="be898-119">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="be898-120">有关更多详细信息，请参阅 <xref:fundamentals/metapackage>。</span><span class="sxs-lookup"><span data-stu-id="be898-120">See <xref:fundamentals/metapackage> for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="be898-121">项目结构差异</span><span class="sxs-lookup"><span data-stu-id="be898-121">Project structure differences</span></span>

<span data-ttu-id="be898-122">ASP.NET Core 中简化了 .csproj 文件格式。</span><span class="sxs-lookup"><span data-stu-id="be898-122">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="be898-123">下面是一些显著的更改：</span><span class="sxs-lookup"><span data-stu-id="be898-123">Some notable changes include:</span></span>

* <span data-ttu-id="be898-124">无需显式添加，即可将文件视作项目的一部分。</span><span class="sxs-lookup"><span data-stu-id="be898-124">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="be898-125">服务于大型团队时，这可减少出现 XML 合并冲突的风险。</span><span class="sxs-lookup"><span data-stu-id="be898-125">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
* <span data-ttu-id="be898-126">没有对其他项目的基于 GUID 的引用，这可以提高文件的可读性。</span><span class="sxs-lookup"><span data-stu-id="be898-126">There are no GUID-based references to other projects, which improves file readability.</span></span>
* <span data-ttu-id="be898-127">无需在 Visual Studio 中卸载文件即可对它进行编辑：</span><span class="sxs-lookup"><span data-stu-id="be898-127">The file can be edited without unloading it in Visual Studio:</span></span>

  ![Visual Studio 2017 中的“编辑 CSPROJ”上下文菜单选项](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="be898-129">Global.asax 文件替换</span><span class="sxs-lookup"><span data-stu-id="be898-129">Global.asax file replacement</span></span>

<span data-ttu-id="be898-130">ASP.NET Core 引入了启动应用的新机制。</span><span class="sxs-lookup"><span data-stu-id="be898-130">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="be898-131">ASP.NET 应用程序的入口点是 Global.asax 文件。</span><span class="sxs-lookup"><span data-stu-id="be898-131">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="be898-132">路由配置及筛选器和区域注册等任务在 Global.asax 文件中进行处理。</span><span class="sxs-lookup"><span data-stu-id="be898-132">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="be898-133">此方法会将应用程序和应用程序要部署到的服务器耦合在一起，并且它们的耦合方式会干扰实现。</span><span class="sxs-lookup"><span data-stu-id="be898-133">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="be898-134">为了将它们分离，引入了 [OWIN](https://owin.org/) 来提供一种更为简便的同时使用多个框架的方法。</span><span class="sxs-lookup"><span data-stu-id="be898-134">In an effort to decouple, [OWIN](https://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="be898-135">OWIN 提供了一个管道，可以只添加所需的模块。</span><span class="sxs-lookup"><span data-stu-id="be898-135">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="be898-136">托管环境使用 [Startup](xref:fundamentals/startup) 函数配置服务和应用的请求管道。</span><span class="sxs-lookup"><span data-stu-id="be898-136">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="be898-137">`Startup` 在应用程序中注册一组中间件。</span><span class="sxs-lookup"><span data-stu-id="be898-137">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="be898-138">对于每个请求，应用程序都使用现有处理程序集的链接列表的头指针调用各个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="be898-138">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="be898-139">每个中间件组件可以向请求处理管道添加一个或多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="be898-139">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="be898-140">为此，需要返回对成为列表新头的处理程序的引用。</span><span class="sxs-lookup"><span data-stu-id="be898-140">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="be898-141">每个处理程序负责记住并调用列表中的下一个处理程序。</span><span class="sxs-lookup"><span data-stu-id="be898-141">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="be898-142">使用 ASP.NET Core 时，应用程序的入口点是 `Startup`，不再具有 Global.asax 的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="be898-142">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="be898-143">结合使用 OWIN 和 .NET Framework 时，使用的管道应如下所示：</span><span class="sxs-lookup"><span data-stu-id="be898-143">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="be898-144">这会配置默认路由，默认为 XmlSerialization 而不是 Json。</span><span class="sxs-lookup"><span data-stu-id="be898-144">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="be898-145">根据需要向此管道添加其他中间件（加载服务、配置设置、静态文件等）。</span><span class="sxs-lookup"><span data-stu-id="be898-145">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="be898-146">ASP.NET Core 使用相似的方法，但是不依赖 OWIN 处理条目。</span><span class="sxs-lookup"><span data-stu-id="be898-146">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="be898-147">而是通过 Program.cs `Main` 方法（类似于控制台应用程序）来完成，并且 `Startup` 会通过该处进行加载。</span><span class="sxs-lookup"><span data-stu-id="be898-147">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="be898-148">`Startup` 必须包含 `Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="be898-148">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="be898-149">在 `Configure` 中，向管道添加必要的中间件。</span><span class="sxs-lookup"><span data-stu-id="be898-149">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="be898-150">在下列示例（位于默认的网站模板）中，使用了多个扩展方法为管道配置对以下内容的支持：</span><span class="sxs-lookup"><span data-stu-id="be898-150">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="be898-151">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="be898-151">BrowserLink</span></span>](https://vswebessentials.com/features/browserlink)
* <span data-ttu-id="be898-152">错误页</span><span class="sxs-lookup"><span data-stu-id="be898-152">Error pages</span></span>
* <span data-ttu-id="be898-153">静态文件</span><span class="sxs-lookup"><span data-stu-id="be898-153">Static files</span></span>
* <span data-ttu-id="be898-154">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="be898-154">ASP.NET Core MVC</span></span>
* <span data-ttu-id="be898-155">身份</span><span class="sxs-lookup"><span data-stu-id="be898-155">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="be898-156">现在主机和应用程序已分离，这样将来就可以灵活地迁移到其他平台。</span><span class="sxs-lookup"><span data-stu-id="be898-156">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

<span data-ttu-id="be898-157">有关 ASP.NET Core 启动和中间件的更深入的参考信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="be898-157">For a more in-depth reference to ASP.NET Core Startup and Middleware, see <xref:fundamentals/startup>.</span></span>

## <a name="storing-configurations"></a><span data-ttu-id="be898-158">存储配置</span><span class="sxs-lookup"><span data-stu-id="be898-158">Storing configurations</span></span>

<span data-ttu-id="be898-159">ASP.NET 支持存储设置。</span><span class="sxs-lookup"><span data-stu-id="be898-159">ASP.NET supports storing settings.</span></span> <span data-ttu-id="be898-160">这些设置可用于支持应用程序已部署到的环境（以此用途为例）。</span><span class="sxs-lookup"><span data-stu-id="be898-160">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="be898-161">常见做法是将所有的自定义键值对存储在 Web.config`<appSettings>`*文件的* 部分中：</span><span class="sxs-lookup"><span data-stu-id="be898-161">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="be898-162">应用程序使用 `ConfigurationManager.AppSettings` 命名空间中的 `System.Configuration` 集合读取这些设置：</span><span class="sxs-lookup"><span data-stu-id="be898-162">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="be898-163">ASP.NET Core 可以将应用程序的配置数据存储在任何文件中，并可在启动中间件的过程中加载它们。</span><span class="sxs-lookup"><span data-stu-id="be898-163">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="be898-164">项目模板中使用的默认文件是 appsettings.json：</span><span class="sxs-lookup"><span data-stu-id="be898-164">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="be898-165">将此文件加载到应用程序内的 `IConfiguration` 的实例的过程在 Startup.cs 中完成：</span><span class="sxs-lookup"><span data-stu-id="be898-165">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="be898-166">应用读取 `Configuration` 来获得这些设置：</span><span class="sxs-lookup"><span data-stu-id="be898-166">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="be898-167">此方法有扩展项，它们可使此过程更加可靠，例如使用[依存关系注入](xref:fundamentals/dependency-injection) (DI) 来加载使用这些值的服务。</span><span class="sxs-lookup"><span data-stu-id="be898-167">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="be898-168">DI 方法提供了一组强类型的配置对象。</span><span class="sxs-lookup"><span data-stu-id="be898-168">The DI approach provides a strongly-typed set of configuration objects.</span></span>

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

<span data-ttu-id="be898-169">**注意：** 有关 ASP.NET Core 配置的更深入的参考信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="be898-169">**Note:** For a more in-depth reference to ASP.NET Core configuration, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="be898-170">本机依存关系注入</span><span class="sxs-lookup"><span data-stu-id="be898-170">Native dependency injection</span></span>

<span data-ttu-id="be898-171">生成大型可缩放应用程序时，一个重要的目标是将组件和服务松散耦合。</span><span class="sxs-lookup"><span data-stu-id="be898-171">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="be898-172">[依赖关系注入](xref:fundamentals/dependency-injection)是实现此项的一种常用技术，它是 ASP.NET Core 的本机组件。</span><span class="sxs-lookup"><span data-stu-id="be898-172">[Dependency injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="be898-173">在 ASP.NET 应用程序中，开发人员依赖第三方库来实现依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="be898-173">In ASP.NET applications, developers rely on a third-party library to implement dependency injection.</span></span> <span data-ttu-id="be898-174">其中的一个库是 Microsoft 模式和做法提供的 [Unity](https://github.com/unitycontainer/unity)。</span><span class="sxs-lookup"><span data-stu-id="be898-174">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="be898-175">使用 Unity 设置依赖关系注入的一个示例是实现 `IDependencyResolver` 包装 `UnityContainer`：</span><span class="sxs-lookup"><span data-stu-id="be898-175">An example of setting up dependency injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](samples/sample8.cs)]

<span data-ttu-id="be898-176">创建 `UnityContainer` 的实例，注册服务，然后将 `HttpConfiguration` 的依赖关系解析程序设置为容器的 `UnityResolver` 新实例：</span><span class="sxs-lookup"><span data-stu-id="be898-176">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](samples/sample9.cs)]

<span data-ttu-id="be898-177">在必要时注入 `IProductRepository`：</span><span class="sxs-lookup"><span data-stu-id="be898-177">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](samples/sample5.cs)]

<span data-ttu-id="be898-178">由于依赖关系注入是 ASP.NET Core 的一部分，因此你可以将服务添加到 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="be898-178">Because dependency injection is part of ASP.NET Core, you can add your service in the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="be898-179">可在任意位置注入存储库，Unity 亦是如此。</span><span class="sxs-lookup"><span data-stu-id="be898-179">The repository can be injected anywhere, as was true with Unity.</span></span>

<span data-ttu-id="be898-180">有关 ASP.NET Core 中的依赖关系注入的详细信息，请参阅 <xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="be898-180">For more information on dependency injection in ASP.NET Core, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="serving-static-files"></a><span data-ttu-id="be898-181">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="be898-181">Serving static files</span></span>

<span data-ttu-id="be898-182">Web 开发的一个重要环节是提供客户端静态资产的功能。</span><span class="sxs-lookup"><span data-stu-id="be898-182">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="be898-183">HTML、CSS、Javascript 和图像是最常见的静态文件示例。</span><span class="sxs-lookup"><span data-stu-id="be898-183">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="be898-184">这些文件需要保存在应用（或 CDN）的发布位置中，并且需要引用它们，以便请求可以加载这些文件。</span><span class="sxs-lookup"><span data-stu-id="be898-184">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="be898-185">在 ASP.NET Core 中，此过程发生了变化。</span><span class="sxs-lookup"><span data-stu-id="be898-185">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="be898-186">在 ASP.NET 中，静态文件存储在各种目录中，并在视图中进行引用。</span><span class="sxs-lookup"><span data-stu-id="be898-186">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="be898-187">在 ASP.NET Core 中，静态文件存储在“Web 根”（*内容根&lt;/wwwroot&gt;* ）中，除非另有配置。</span><span class="sxs-lookup"><span data-stu-id="be898-187">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="be898-188">通过从 `UseStaticFiles` 调用 `Startup.Configure` 扩展方法将这些文件加载到请求管道中：</span><span class="sxs-lookup"><span data-stu-id="be898-188">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

<span data-ttu-id="be898-189">注意：如果面向 .NET Framework，则安装 NuGet 包 `Microsoft.AspNetCore.StaticFiles`。</span><span class="sxs-lookup"><span data-stu-id="be898-189">**Note:** If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="be898-190">例如，可以通过浏览器从类似 *的位置访问 wwwroot/images*`http://<app>/images/<imageFileName>`文件夹中的图像资产。</span><span class="sxs-lookup"><span data-stu-id="be898-190">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

<span data-ttu-id="be898-191">**注意：** 有关在 ASP.NET Core 中提供静态文件的更深入的参考信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="be898-191">**Note:** For a more in-depth reference to serving static files in ASP.NET Core, see <xref:fundamentals/static-files>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be898-192">其他资源</span><span class="sxs-lookup"><span data-stu-id="be898-192">Additional resources</span></span>

* [<span data-ttu-id="be898-193">将库移植到 .NET Core</span><span class="sxs-lookup"><span data-stu-id="be898-193">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
