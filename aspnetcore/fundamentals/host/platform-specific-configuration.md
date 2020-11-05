---
title: 在 ASP.NET Core 中使用承载启动程序集
author: rick-anderson
description: 了解如何使用 IHostingStartup 从外部程序集增强 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c12487875db69472ee328dfc7a611ee99974c770
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061049"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="69b1f-103">在 ASP.NET Core 中使用承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="69b1f-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="69b1f-104">作者：[Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="69b1f-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="69b1f-105">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>（承载启动）实现，在启动时从外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="69b1f-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="69b1f-106">例如，外部库可使用承载启动实现为应用提供其他配置提供程序或服务。</span><span class="sxs-lookup"><span data-stu-id="69b1f-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="69b1f-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="69b1f-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="69b1f-108">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="69b1f-108">HostingStartup attribute</span></span>

<span data-ttu-id="69b1f-109">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性表示存在要在运行时激活的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="69b1f-110">将自动扫描输入程序集或包含 `Startup` 类的程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-111">用于搜索 `HostingStartup` 属性的程序集列表在运行时从 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 中的配置加载。</span><span class="sxs-lookup"><span data-stu-id="69b1f-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="69b1f-112">要从发现中排除的程序集列表从 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) 加载。</span><span class="sxs-lookup"><span data-stu-id="69b1f-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="69b1f-113">在以下示例中，承载启动程序集的命名空间为 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="69b1f-114">包含承载启动代码的类是 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="69b1f-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="69b1f-115">`HostingStartup` 属性通常位于承载启动程序集的 `IHostingStartup` 实现类文件中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="69b1f-116">发现加载的承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="69b1f-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="69b1f-117">要发现加载的承载启动程序集，请启用日志记录并检查应用的日志。</span><span class="sxs-lookup"><span data-stu-id="69b1f-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="69b1f-118">记录加载程序集时发生的错误。</span><span class="sxs-lookup"><span data-stu-id="69b1f-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="69b1f-119">会在调试级别记录加载的承载启动程序集，并记录所有错误。</span><span class="sxs-lookup"><span data-stu-id="69b1f-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="69b1f-120">禁用承载启动程序集的自动加载</span><span class="sxs-lookup"><span data-stu-id="69b1f-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="69b1f-121">要禁用承载启动程序集的自动加载，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="69b1f-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="69b1f-122">要阻止加载所有承载启动程序集，请将以下内容之一设置为 `true` 或 `1`：</span><span class="sxs-lookup"><span data-stu-id="69b1f-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="69b1f-123">阻止承载启动主机配置设置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="69b1f-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="69b1f-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="69b1f-125">要阻止加载特定的承载启动程序集，请将以下之一设置为以分号分隔的承载启动程序集字符串，以便在启动时排除：</span><span class="sxs-lookup"><span data-stu-id="69b1f-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="69b1f-126">承载启动排除程序集承载配置设置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="69b1f-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="69b1f-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="69b1f-128">如果同时设置了主机配置设置和环境变量，则主机设置将控制行为。</span><span class="sxs-lookup"><span data-stu-id="69b1f-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="69b1f-129">如果使用主机设置或环境变量来禁用承载启动程序集，将全局禁用程序集，并可能会禁用应用的多个特征。</span><span class="sxs-lookup"><span data-stu-id="69b1f-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="69b1f-130">项目</span><span class="sxs-lookup"><span data-stu-id="69b1f-130">Project</span></span>

<span data-ttu-id="69b1f-131">使用以下任一项目类型创建承载启动：</span><span class="sxs-lookup"><span data-stu-id="69b1f-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="69b1f-132">类库</span><span class="sxs-lookup"><span data-stu-id="69b1f-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="69b1f-133">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="69b1f-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="69b1f-134">类库</span><span class="sxs-lookup"><span data-stu-id="69b1f-134">Class library</span></span>

<span data-ttu-id="69b1f-135">可在类库中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="69b1f-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="69b1f-136">库包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="69b1f-137">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)包括 :::no-loc(Razor)::: Pages 应用、HostingStartupApp 和类库 HostingStartupLibrary 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a :::no-loc(Razor)::: Pages app, *HostingStartupApp* , and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="69b1f-138">类库：</span><span class="sxs-lookup"><span data-stu-id="69b1f-138">The class library:</span></span>

* <span data-ttu-id="69b1f-139">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="69b1f-140">`ServiceKeyInjection` 使用内存中配置提供程序 ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="69b1f-141">包含 `HostingStartup` 属性，用于标识承载启动的命名空间和类。</span><span class="sxs-lookup"><span data-stu-id="69b1f-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="69b1f-142">`ServiceKeyInjection` 类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="69b1f-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="69b1f-143">HostingStartupLibrary/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-143">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="69b1f-144">应用的索引页读取并呈现类库承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="69b1f-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="69b1f-145">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-145">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="69b1f-146">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)还包括一个 NuGet 包项目，该项目提供单独的承载启动 HostingStartupPackage。</span><span class="sxs-lookup"><span data-stu-id="69b1f-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="69b1f-147">该包具有与前述类库相同的特征。</span><span class="sxs-lookup"><span data-stu-id="69b1f-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="69b1f-148">包：</span><span class="sxs-lookup"><span data-stu-id="69b1f-148">The package:</span></span>

* <span data-ttu-id="69b1f-149">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="69b1f-150">`ServiceKeyInjection` 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="69b1f-151">包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="69b1f-152">HostingStartupPackage/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-152">*HostingStartupPackage/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="69b1f-153">应用的索引页读取并呈现包承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="69b1f-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="69b1f-154">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-154">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="69b1f-155">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="69b1f-155">Console app without an entry point</span></span>

<span data-ttu-id="69b1f-156">此方法仅适用于 .NET Core 应用，不适用于 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="69b1f-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="69b1f-157">可在包含 `HostingStartup` 属性的无入口点的控制台应用中提供动态承载启动增强功能，该功能无需编译时引用进行激活。</span><span class="sxs-lookup"><span data-stu-id="69b1f-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-158">发布控制台应用会生成可从运行时存储中使用的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="69b1f-159">此过程中使用没有入口点的控制台应用，因为：</span><span class="sxs-lookup"><span data-stu-id="69b1f-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="69b1f-160">需要依赖项文件来使用承载启动程序集中的承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="69b1f-161">依赖项文件是一种可运行的应用资产，它通过发布应用而不是库来生成。</span><span class="sxs-lookup"><span data-stu-id="69b1f-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="69b1f-162">无法直接将库添加到[运行时包存储](/dotnet/core/deploying/runtime-store)，该过程需要一个以已共享运行时为目标的可运行项目。</span><span class="sxs-lookup"><span data-stu-id="69b1f-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="69b1f-163">创建动态承载启动过程中：</span><span class="sxs-lookup"><span data-stu-id="69b1f-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="69b1f-164">从控制台应用创建承载启动程序集，无需以下入口点：</span><span class="sxs-lookup"><span data-stu-id="69b1f-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="69b1f-165">包含 `IHostingStartup` 实现的类。</span><span class="sxs-lookup"><span data-stu-id="69b1f-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="69b1f-166">包含用于识别 `IHostingStartup` 实现类的 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="69b1f-167">发布控制台应用，获取承载启动的依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="69b1f-168">发布控制台应用的结果是从依赖项文件中删除了未使用的依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="69b1f-169">修改依赖项文件以设置承载启动程序集的运行时位置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="69b1f-170">承载启动程序集及其依赖项文件位于运行时包存储中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="69b1f-171">要发现承载启动程序集及其依赖项文件，它们将在一对环境变量中列出。</span><span class="sxs-lookup"><span data-stu-id="69b1f-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="69b1f-172">控制台应用引用 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 包：</span><span class="sxs-lookup"><span data-stu-id="69b1f-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="69b1f-173">生成 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 时，[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性将类标识为 `IHostingStartup` 的实现，用于加载和执行。</span><span class="sxs-lookup"><span data-stu-id="69b1f-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="69b1f-174">在下面的示例中，命名空间为 `StartupEnhancement`，类为 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="69b1f-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="69b1f-175">类实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="69b1f-176">该类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="69b1f-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="69b1f-177">用户代码中 `Startup.Configure` 之前的运行时调用托管启动程序集中的 `IHostingStartup.Configure`，允许用户代码覆盖托管启动程序集提供的任何配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="69b1f-178">生成 `IHostingStartup` 项目时，依赖项文件 (.deps.json) 将程序集的 `runtime` 位置设为 bin 文件夹 ：</span><span class="sxs-lookup"><span data-stu-id="69b1f-178">When building an `IHostingStartup` project, the dependencies file ( *.deps.json* ) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="69b1f-179">仅显示部分文件。</span><span class="sxs-lookup"><span data-stu-id="69b1f-179">Only part of the file is shown.</span></span> <span data-ttu-id="69b1f-180">示例中程序集的名称是 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="69b1f-181">托管启动提供的配置</span><span class="sxs-lookup"><span data-stu-id="69b1f-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="69b1f-182">处理配置有两种方法，具体取决于是希望托管启动的配置优先还是应用的配置优先：</span><span class="sxs-lookup"><span data-stu-id="69b1f-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="69b1f-183">使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行后加载配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="69b1f-184">使用此方法，托管启动配置优先于应用的配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="69b1f-185">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行之前加载配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="69b1f-186">使用此方法，应用的配置值优先于托管启动程序提供的值。</span><span class="sxs-lookup"><span data-stu-id="69b1f-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="69b1f-187">指定承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="69b1f-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="69b1f-188">对于类库或控制台应用提供的承载启动，请在 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中指定承载启动程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="69b1f-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="69b1f-189">环境变量是以分号分隔的程序集列表。</span><span class="sxs-lookup"><span data-stu-id="69b1f-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="69b1f-190">仅扫描承载启动程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-191">对于示例应用 HostingStartupApp，要发现前述的承载启动，请将环境变量设置为以下值：</span><span class="sxs-lookup"><span data-stu-id="69b1f-191">For the sample app, *HostingStartupApp* , to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="69b1f-192">还可使用承载启动程序集主机配置设置来设置此承载启动程序集：</span><span class="sxs-lookup"><span data-stu-id="69b1f-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="69b1f-193">存在多个托管启动程序集时，将按列出程序集的顺序执行 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法。</span><span class="sxs-lookup"><span data-stu-id="69b1f-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="69b1f-194">激活</span><span class="sxs-lookup"><span data-stu-id="69b1f-194">Activation</span></span>

<span data-ttu-id="69b1f-195">承载启动激活的选项包括：</span><span class="sxs-lookup"><span data-stu-id="69b1f-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="69b1f-196">[运行时存储](#runtime-store)：激活无需用于激活的编译时引用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="69b1f-197">示例应用将承载启动程序集和依赖项文件放入文件夹“deployment”，以便在多计算机环境中部署承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment* , to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="69b1f-198">“deployment”文件夹还包括 PowerShell 脚本，该脚本可在部署系统上创建或修改环境变量以启用承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="69b1f-199">激活所需的编译时引用</span><span class="sxs-lookup"><span data-stu-id="69b1f-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="69b1f-200">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="69b1f-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="69b1f-201">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="69b1f-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="69b1f-202">运行时存储</span><span class="sxs-lookup"><span data-stu-id="69b1f-202">Runtime store</span></span>

<span data-ttu-id="69b1f-203">承载启动实现位于[运行时存储](/dotnet/core/deploying/runtime-store)中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="69b1f-204">增强型应用无需对程序集进行编译时引用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="69b1f-205">构建承载启动后，使用清单项目文件和 [dotnet store](/dotnet/core/tools/dotnet-store) 命令生成运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="69b1f-206">在示例应用（ *RuntimeStore* 项目）中，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="69b1f-206">In the sample app ( *RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="69b1f-207">对于发现运行时存储的运行时，运行时存储的位置将添加到 `DOTNET_SHARED_STORE` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="69b1f-208">**修改并放置承载启动的依赖项文件**</span><span class="sxs-lookup"><span data-stu-id="69b1f-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="69b1f-209">要在未包引用增强功能的情况下激活增强功能，请使用 `additionalDeps` 为运行时指定附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="69b1f-210">使用 `additionalDeps`，你可以：</span><span class="sxs-lookup"><span data-stu-id="69b1f-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="69b1f-211">通过提供一组附加的 .deps.json 文件来扩展应用的库图，以便在启动时与应用自身的 .deps.json 文件合并 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="69b1f-212">使承载启动程序集可被发现并可加载。</span><span class="sxs-lookup"><span data-stu-id="69b1f-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="69b1f-213">生成附加依赖项文件的推荐方法是：</span><span class="sxs-lookup"><span data-stu-id="69b1f-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="69b1f-214">对上一节中引用的运行时存储清单文件执行 `dotnet publish`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="69b1f-215">从库中删除清单引用，以及生成的 deps.json 文件的 `runtime` 部分。</span><span class="sxs-lookup"><span data-stu-id="69b1f-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="69b1f-216">在示例项目中，`store.manifest/1.0.0` 属性已从 `targets` 和 `libraries` 部分中删除：</span><span class="sxs-lookup"><span data-stu-id="69b1f-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="69b1f-217">将 .deps.json 文件放入以下位置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="69b1f-218">`{ADDITIONAL DEPENDENCIES PATH}`：添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量的位置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="69b1f-219">`{SHARED FRAMEWORK NAME}`：此附加依赖项文件所必需的共享框架。</span><span class="sxs-lookup"><span data-stu-id="69b1f-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="69b1f-220">`{SHARED FRAMEWORK VERSION}`：最低共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="69b1f-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="69b1f-221">`{ENHANCEMENT ASSEMBLY NAME}`：增强程序集名称。</span><span class="sxs-lookup"><span data-stu-id="69b1f-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="69b1f-222">在示例应用（ *RuntimeStore*  项目）中，附加依赖项文件放于以下位置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-222">In the sample app ( *RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="69b1f-223">对于发现运行时存储位置的运行时，附加依赖项文件位置将添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="69b1f-224">在示例应用（ *RuntimeStore* 项目）中，使用 [PowerShell](/powershell/scripting/powershell-scripting) 脚本完成构建运行时存储并生成附加依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="69b1f-224">In the sample app ( *RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="69b1f-225">有关如何设置各种操作系统的环境变量的示例，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="69b1f-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="69b1f-226">**部署**</span><span class="sxs-lookup"><span data-stu-id="69b1f-226">**Deployment**</span></span>

<span data-ttu-id="69b1f-227">为了便于在多计算机环境中部署托承载启动，示例应用在已发布的输出中创建一个“deployment”文件夹，其中包含：</span><span class="sxs-lookup"><span data-stu-id="69b1f-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="69b1f-228">承载启动运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="69b1f-229">承载启动依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="69b1f-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="69b1f-230">PowerShell 脚本，用于创建或修改 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE` 和 `DOTNET_ADDITIONAL_DEPS` 以支持激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="69b1f-231">从部署系统上的管理 PowerShell 命令提示符运行脚本。</span><span class="sxs-lookup"><span data-stu-id="69b1f-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="69b1f-232">NuGet 程序包</span><span class="sxs-lookup"><span data-stu-id="69b1f-232">NuGet package</span></span>

<span data-ttu-id="69b1f-233">可在 NuGet 包中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="69b1f-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="69b1f-234">该包含有 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-235">包提供的承载启动类型使用以下任一方法提供给应用：</span><span class="sxs-lookup"><span data-stu-id="69b1f-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="69b1f-236">增强型应用的项目文件在应用的项目文件（编译时引用）中为承载启动提供了包引用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="69b1f-237">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="69b1f-238">此方法适用于已发布到 [nuget.org](https://www.nuget.org/) 的承载启动程序集包。</span><span class="sxs-lookup"><span data-stu-id="69b1f-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="69b1f-239">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="69b1f-240">有关 NuGet 包和运行时存储的详细信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="69b1f-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="69b1f-241">如何使用跨平台工具创建 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="69b1f-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="69b1f-242">发布包</span><span class="sxs-lookup"><span data-stu-id="69b1f-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="69b1f-243">运行时包存储</span><span class="sxs-lookup"><span data-stu-id="69b1f-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="69b1f-244">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="69b1f-244">Project bin folder</span></span>

<span data-ttu-id="69b1f-245">承载启动增强功能可通过增强型应用中的 bin -deployed 程序集提供。</span><span class="sxs-lookup"><span data-stu-id="69b1f-245">A hosting startup enhancement can be provided by a *bin* -deployed assembly in the enhanced app.</span></span> <span data-ttu-id="69b1f-246">程序集提供的承载启动类型使用以下方法之一提供给应用：</span><span class="sxs-lookup"><span data-stu-id="69b1f-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="69b1f-247">增强型应用的项目文件对承载启动进行了程序集引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="69b1f-248">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="69b1f-249">当部署方案要求对承载启动程序集（.dll 文件）进行编译时引用并将程序集移动到以下任一位置时，此方法适用：</span><span class="sxs-lookup"><span data-stu-id="69b1f-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly ( *.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="69b1f-250">进行中的项目。</span><span class="sxs-lookup"><span data-stu-id="69b1f-250">The consuming project.</span></span>
  * <span data-ttu-id="69b1f-251">进行中的项目可访问的位置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="69b1f-252">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="69b1f-253">在以 .NET Framework 为目标时，程序集可在默认加载上下文中加载，这在 .NET Framework 上表示程序集位于以下任一位置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="69b1f-254">应用程序基路径：应用的可执行文件 (.exe) 所在的 bin 文件夹。</span><span class="sxs-lookup"><span data-stu-id="69b1f-254">Application base path: The *bin* folder where the app's executable ( *.exe* ) is located.</span></span>
  * <span data-ttu-id="69b1f-255">全局程序集缓存 (GAC)：GAC 存储多个 .NET Framework 应用共享的程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="69b1f-256">有关详细信息，请参阅[如何：将程序集安装到 .NET Framework 文档中的全局程序集缓存](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="69b1f-257">示例代码</span><span class="sxs-lookup"><span data-stu-id="69b1f-257">Sample code</span></span>

<span data-ttu-id="69b1f-258">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）演示了承载启动实现方案：</span><span class="sxs-lookup"><span data-stu-id="69b1f-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="69b1f-259">两个承载启动程序集（类库）分别设置一对内存中配置键值对：</span><span class="sxs-lookup"><span data-stu-id="69b1f-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="69b1f-260">NuGet 包 (HostingStartupPackage)</span><span class="sxs-lookup"><span data-stu-id="69b1f-260">NuGet package ( *HostingStartupPackage* )</span></span>
  * <span data-ttu-id="69b1f-261">类库 (HostingStartupLibrary)</span><span class="sxs-lookup"><span data-stu-id="69b1f-261">Class library ( *HostingStartupLibrary* )</span></span>
* <span data-ttu-id="69b1f-262">从运行时存储部署的程序集 (StartupDiagnostics) 激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-262">A hosting startup is activated from a runtime store-deployed assembly ( *StartupDiagnostics* ).</span></span> <span data-ttu-id="69b1f-263">该程序集在启动时将两个中间件添加到应用，用于提供以下内容的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="69b1f-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="69b1f-264">已注册服务</span><span class="sxs-lookup"><span data-stu-id="69b1f-264">Registered services</span></span>
  * <span data-ttu-id="69b1f-265">地址（方案、主机、基路径、路径、查询字符串）</span><span class="sxs-lookup"><span data-stu-id="69b1f-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="69b1f-266">连接（远程 IP、远程端口、本地 IP、本地端口、客户端证书）</span><span class="sxs-lookup"><span data-stu-id="69b1f-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="69b1f-267">请求标头</span><span class="sxs-lookup"><span data-stu-id="69b1f-267">Request headers</span></span>
  * <span data-ttu-id="69b1f-268">环境变量</span><span class="sxs-lookup"><span data-stu-id="69b1f-268">Environment variables</span></span>

<span data-ttu-id="69b1f-269">运行示例：</span><span class="sxs-lookup"><span data-stu-id="69b1f-269">To run the sample:</span></span>

<span data-ttu-id="69b1f-270">**从 NuGet 包激活**</span><span class="sxs-lookup"><span data-stu-id="69b1f-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="69b1f-271">使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令编译 HostingStartupPackage 包。</span><span class="sxs-lookup"><span data-stu-id="69b1f-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="69b1f-272">将包的程序集名称 HostingStartupPackage 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="69b1f-273">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-273">Compile and run the app.</span></span> <span data-ttu-id="69b1f-274">增强型应用中存在包引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="69b1f-275">应用项目文件中的 `<PropertyGroup>` 指定包项目的输出 (../HostingStartupPackage/bin/Debug) 作为包源。</span><span class="sxs-lookup"><span data-stu-id="69b1f-275">A `<PropertyGroup>` in the app's project file specifies the package project's output ( *../HostingStartupPackage/bin/Debug* ) as a package source.</span></span> <span data-ttu-id="69b1f-276">这允许应用使用该包而无需将包上传到 [nuget.org](https://www.nuget.org/)。有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="69b1f-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="69b1f-277">观察到索引页呈现的服务配置键值与包的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="69b1f-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="69b1f-278">如果更改并重新编译 HostingStartupPackage 项目，请清除本地 NuGet 包缓存，确保 HostingStartupApp 从本地缓存中收到更新后的包而不是旧包 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="69b1f-279">要清除本地 NuGet 缓存，请执行以下 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 命令：</span><span class="sxs-lookup"><span data-stu-id="69b1f-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="69b1f-280">**从类库激活**</span><span class="sxs-lookup"><span data-stu-id="69b1f-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="69b1f-281">使用 [dotnet build](/dotnet/core/tools/dotnet-build) 命令编译 HostingStartupLibrary 类库。</span><span class="sxs-lookup"><span data-stu-id="69b1f-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="69b1f-282">将类库的程序集名称 HostingStartupLibrary 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="69b1f-283">bin - 通过将类库编译输出中的 HostingStartupLibrary.dll 文件复制到应用的 bin/Debug 文件夹，将类库程序集部署到应用  。</span><span class="sxs-lookup"><span data-stu-id="69b1f-283">*bin* -deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="69b1f-284">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-284">Compile and run the app.</span></span> <span data-ttu-id="69b1f-285">应用项目文件中的 `<ItemGroup>` 引用类库的程序集 (.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll)（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-285">An `<ItemGroup>` in the app's project file references the class library's assembly ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) (a compile-time reference).</span></span> <span data-ttu-id="69b1f-286">有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="69b1f-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="69b1f-287">观察到索引页呈现的服务配置键值与类库的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="69b1f-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="69b1f-288">**从运行时存储部署的程序集激活**</span><span class="sxs-lookup"><span data-stu-id="69b1f-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="69b1f-289">StartupDiagnostics 项目使用 [PowerShell](/powershell/scripting/powershell-scripting) 修改其 StartupDiagnostics.deps.json 文件 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="69b1f-290">默认情况下，Windows 7 SP1 和 Windows Server 2008 R2 SP1 及以后版本的 Windows 上安装有 PowerShell。</span><span class="sxs-lookup"><span data-stu-id="69b1f-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="69b1f-291">若要在其他平台上获取 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。</span><span class="sxs-lookup"><span data-stu-id="69b1f-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="69b1f-292">执行 RuntimeStore文件夹中的 build.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="69b1f-293">脚本：</span><span class="sxs-lookup"><span data-stu-id="69b1f-293">The script:</span></span>
   * <span data-ttu-id="69b1f-294">在 obj\packages 文件夹中生成 `StartupDiagnostics` 包。</span><span class="sxs-lookup"><span data-stu-id="69b1f-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="69b1f-295">在 store 文件夹中生成 `StartupDiagnostics` 的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="69b1f-296">该脚本中的 `dotnet store` 命令使用 `win7-x64` [运行时标识符 (RID)](/dotnet/core/rid-catalog) 将托管启动部署到 Windows。</span><span class="sxs-lookup"><span data-stu-id="69b1f-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="69b1f-297">为其他运行时提供托管启动程序集时，在脚本第 37 行上替换为正确的 RID。</span><span class="sxs-lookup"><span data-stu-id="69b1f-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="69b1f-298">`StartupDiagnostics` 的运行时存储稍后会移动到将使用程序集的计算机上的用户或系统的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="69b1f-299">`StartupDiagnostics` 程序集的用户运行时存储安装位置是 .dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll。</span><span class="sxs-lookup"><span data-stu-id="69b1f-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="69b1f-300">在 additionalDeps 文件夹中生成 `StartupDiagnostics` 的 `additionalDeps`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="69b1f-301">附加依赖项稍后会移动到用户或系统的附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="69b1f-302">用户 `StartupDiagnostics` 附加依赖项安装位置是 .dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json。</span><span class="sxs-lookup"><span data-stu-id="69b1f-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="69b1f-303">将 deploy.ps1 文件放置在 deployment 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="69b1f-304">运行 deployment 文件夹中的 deploy.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="69b1f-305">脚本将：</span><span class="sxs-lookup"><span data-stu-id="69b1f-305">The script appends:</span></span>
   * <span data-ttu-id="69b1f-306">`StartupDiagnostics` 追加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="69b1f-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="69b1f-307">`DOTNET_ADDITIONAL_DEPS` 环境变量的承载启动依赖项路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="69b1f-308">`DOTNET_SHARED_STORE` 环境变量的运行时存储路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="69b1f-309">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-309">Run the sample app.</span></span>
1. <span data-ttu-id="69b1f-310">请求 `/services` 终结点以查看应用的注册服务。</span><span class="sxs-lookup"><span data-stu-id="69b1f-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="69b1f-311">请求 `/diag` 终结点以查看诊断信息。</span><span class="sxs-lookup"><span data-stu-id="69b1f-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="69b1f-312">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>（承载启动）实现，在启动时从外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="69b1f-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="69b1f-313">例如，外部库可使用承载启动实现为应用提供其他配置提供程序或服务。</span><span class="sxs-lookup"><span data-stu-id="69b1f-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="69b1f-314">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="69b1f-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="69b1f-315">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="69b1f-315">HostingStartup attribute</span></span>

<span data-ttu-id="69b1f-316">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性表示存在要在运行时激活的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="69b1f-317">将自动扫描输入程序集或包含 `Startup` 类的程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-318">用于搜索 `HostingStartup` 属性的程序集列表在运行时从 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 中的配置加载。</span><span class="sxs-lookup"><span data-stu-id="69b1f-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="69b1f-319">要从发现中排除的程序集列表从 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) 加载。</span><span class="sxs-lookup"><span data-stu-id="69b1f-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="69b1f-320">有关详细信息，请参阅 [Web 主机：承载启动程序集](xref:fundamentals/host/web-host#hosting-startup-assemblies)和 [Web 主机：承载启动排除程序集](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)。</span><span class="sxs-lookup"><span data-stu-id="69b1f-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="69b1f-321">在以下示例中，承载启动程序集的命名空间为 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="69b1f-322">包含承载启动代码的类是 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="69b1f-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="69b1f-323">`HostingStartup` 属性通常位于承载启动程序集的 `IHostingStartup` 实现类文件中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="69b1f-324">发现加载的承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="69b1f-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="69b1f-325">要发现加载的承载启动程序集，请启用日志记录并检查应用的日志。</span><span class="sxs-lookup"><span data-stu-id="69b1f-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="69b1f-326">记录加载程序集时发生的错误。</span><span class="sxs-lookup"><span data-stu-id="69b1f-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="69b1f-327">会在调试级别记录加载的承载启动程序集，并记录所有错误。</span><span class="sxs-lookup"><span data-stu-id="69b1f-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="69b1f-328">禁用承载启动程序集的自动加载</span><span class="sxs-lookup"><span data-stu-id="69b1f-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="69b1f-329">要禁用承载启动程序集的自动加载，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="69b1f-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="69b1f-330">要阻止加载所有承载启动程序集，请将以下内容之一设置为 `true` 或 `1`：</span><span class="sxs-lookup"><span data-stu-id="69b1f-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="69b1f-331">[阻止承载启动](xref:fundamentals/host/web-host#prevent-hosting-startup)主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="69b1f-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="69b1f-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="69b1f-333">要阻止加载特定的承载启动程序集，请将以下之一设置为以分号分隔的承载启动程序集字符串，以便在启动时排除：</span><span class="sxs-lookup"><span data-stu-id="69b1f-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="69b1f-334">[承载启动排除程序集](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)承载配置设置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="69b1f-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="69b1f-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="69b1f-336">如果同时设置了主机配置设置和环境变量，则主机设置将控制行为。</span><span class="sxs-lookup"><span data-stu-id="69b1f-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="69b1f-337">如果使用主机设置或环境变量来禁用承载启动程序集，将全局禁用程序集，并可能会禁用应用的多个特征。</span><span class="sxs-lookup"><span data-stu-id="69b1f-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="69b1f-338">项目</span><span class="sxs-lookup"><span data-stu-id="69b1f-338">Project</span></span>

<span data-ttu-id="69b1f-339">使用以下任一项目类型创建承载启动：</span><span class="sxs-lookup"><span data-stu-id="69b1f-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="69b1f-340">类库</span><span class="sxs-lookup"><span data-stu-id="69b1f-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="69b1f-341">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="69b1f-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="69b1f-342">类库</span><span class="sxs-lookup"><span data-stu-id="69b1f-342">Class library</span></span>

<span data-ttu-id="69b1f-343">可在类库中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="69b1f-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="69b1f-344">库包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="69b1f-345">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)包括 :::no-loc(Razor)::: Pages 应用、HostingStartupApp 和类库 HostingStartupLibrary 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a :::no-loc(Razor)::: Pages app, *HostingStartupApp* , and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="69b1f-346">类库：</span><span class="sxs-lookup"><span data-stu-id="69b1f-346">The class library:</span></span>

* <span data-ttu-id="69b1f-347">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="69b1f-348">`ServiceKeyInjection` 使用内存中配置提供程序 ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="69b1f-349">包含 `HostingStartup` 属性，用于标识承载启动的命名空间和类。</span><span class="sxs-lookup"><span data-stu-id="69b1f-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="69b1f-350">`ServiceKeyInjection` 类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="69b1f-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="69b1f-351">HostingStartupLibrary/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-351">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="69b1f-352">应用的索引页读取并呈现类库承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="69b1f-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="69b1f-353">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-353">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="69b1f-354">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)还包括一个 NuGet 包项目，该项目提供单独的承载启动 HostingStartupPackage。</span><span class="sxs-lookup"><span data-stu-id="69b1f-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="69b1f-355">该包具有与前述类库相同的特征。</span><span class="sxs-lookup"><span data-stu-id="69b1f-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="69b1f-356">包：</span><span class="sxs-lookup"><span data-stu-id="69b1f-356">The package:</span></span>

* <span data-ttu-id="69b1f-357">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="69b1f-358">`ServiceKeyInjection` 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="69b1f-359">包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="69b1f-360">HostingStartupPackage/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-360">*HostingStartupPackage/ServiceKeyInjection.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="69b1f-361">应用的索引页读取并呈现包承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="69b1f-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="69b1f-362">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="69b1f-362">*HostingStartupApp/Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="69b1f-363">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="69b1f-363">Console app without an entry point</span></span>

<span data-ttu-id="69b1f-364">此方法仅适用于 .NET Core 应用，不适用于 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="69b1f-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="69b1f-365">可在包含 `HostingStartup` 属性的无入口点的控制台应用中提供动态承载启动增强功能，该功能无需编译时引用进行激活。</span><span class="sxs-lookup"><span data-stu-id="69b1f-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-366">发布控制台应用会生成可从运行时存储中使用的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="69b1f-367">此过程中使用没有入口点的控制台应用，因为：</span><span class="sxs-lookup"><span data-stu-id="69b1f-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="69b1f-368">需要依赖项文件来使用承载启动程序集中的承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="69b1f-369">依赖项文件是一种可运行的应用资产，它通过发布应用而不是库来生成。</span><span class="sxs-lookup"><span data-stu-id="69b1f-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="69b1f-370">无法直接将库添加到[运行时包存储](/dotnet/core/deploying/runtime-store)，该过程需要一个以已共享运行时为目标的可运行项目。</span><span class="sxs-lookup"><span data-stu-id="69b1f-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="69b1f-371">创建动态承载启动过程中：</span><span class="sxs-lookup"><span data-stu-id="69b1f-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="69b1f-372">从控制台应用创建承载启动程序集，无需以下入口点：</span><span class="sxs-lookup"><span data-stu-id="69b1f-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="69b1f-373">包含 `IHostingStartup` 实现的类。</span><span class="sxs-lookup"><span data-stu-id="69b1f-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="69b1f-374">包含用于识别 `IHostingStartup` 实现类的 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="69b1f-375">发布控制台应用，获取承载启动的依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="69b1f-376">发布控制台应用的结果是从依赖项文件中删除了未使用的依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="69b1f-377">修改依赖项文件以设置承载启动程序集的运行时位置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="69b1f-378">承载启动程序集及其依赖项文件位于运行时包存储中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="69b1f-379">要发现承载启动程序集及其依赖项文件，它们将在一对环境变量中列出。</span><span class="sxs-lookup"><span data-stu-id="69b1f-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="69b1f-380">控制台应用引用 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 包：</span><span class="sxs-lookup"><span data-stu-id="69b1f-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="69b1f-381">生成 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 时，[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性将类标识为 `IHostingStartup` 的实现，用于加载和执行。</span><span class="sxs-lookup"><span data-stu-id="69b1f-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="69b1f-382">在下面的示例中，命名空间为 `StartupEnhancement`，类为 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="69b1f-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="69b1f-383">类实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="69b1f-384">该类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="69b1f-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="69b1f-385">用户代码中 `Startup.Configure` 之前的运行时调用托管启动程序集中的 `IHostingStartup.Configure`，允许用户代码覆盖托管启动程序集提供的任何配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="69b1f-386">生成 `IHostingStartup` 项目时，依赖项文件 (.deps.json) 将程序集的 `runtime` 位置设为 bin 文件夹 ：</span><span class="sxs-lookup"><span data-stu-id="69b1f-386">When building an `IHostingStartup` project, the dependencies file ( *.deps.json* ) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="69b1f-387">仅显示部分文件。</span><span class="sxs-lookup"><span data-stu-id="69b1f-387">Only part of the file is shown.</span></span> <span data-ttu-id="69b1f-388">示例中程序集的名称是 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="69b1f-389">托管启动提供的配置</span><span class="sxs-lookup"><span data-stu-id="69b1f-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="69b1f-390">处理配置有两种方法，具体取决于是希望托管启动的配置优先还是应用的配置优先：</span><span class="sxs-lookup"><span data-stu-id="69b1f-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="69b1f-391">使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行后加载配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="69b1f-392">使用此方法，托管启动配置优先于应用的配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="69b1f-393">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行之前加载配置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="69b1f-394">使用此方法，应用的配置值优先于托管启动程序提供的值。</span><span class="sxs-lookup"><span data-stu-id="69b1f-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="69b1f-395">指定承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="69b1f-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="69b1f-396">对于类库或控制台应用提供的承载启动，请在 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中指定承载启动程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="69b1f-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="69b1f-397">环境变量是以分号分隔的程序集列表。</span><span class="sxs-lookup"><span data-stu-id="69b1f-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="69b1f-398">仅扫描承载启动程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-399">对于示例应用 HostingStartupApp，要发现前述的承载启动，请将环境变量设置为以下值：</span><span class="sxs-lookup"><span data-stu-id="69b1f-399">For the sample app, *HostingStartupApp* , to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="69b1f-400">还可使用[承载启动程序集](xref:fundamentals/host/web-host#hosting-startup-assemblies)主机配置设置来设置此承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="69b1f-401">存在多个托管启动程序集时，将按列出程序集的顺序执行 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法。</span><span class="sxs-lookup"><span data-stu-id="69b1f-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="69b1f-402">激活</span><span class="sxs-lookup"><span data-stu-id="69b1f-402">Activation</span></span>

<span data-ttu-id="69b1f-403">承载启动激活的选项包括：</span><span class="sxs-lookup"><span data-stu-id="69b1f-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="69b1f-404">[运行时存储](#runtime-store)：激活无需用于激活的编译时引用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="69b1f-405">示例应用将承载启动程序集和依赖项文件放入文件夹“deployment”，以便在多计算机环境中部署承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment* , to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="69b1f-406">“deployment”文件夹还包括 PowerShell 脚本，该脚本可在部署系统上创建或修改环境变量以启用承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="69b1f-407">激活所需的编译时引用</span><span class="sxs-lookup"><span data-stu-id="69b1f-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="69b1f-408">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="69b1f-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="69b1f-409">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="69b1f-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="69b1f-410">运行时存储</span><span class="sxs-lookup"><span data-stu-id="69b1f-410">Runtime store</span></span>

<span data-ttu-id="69b1f-411">承载启动实现位于[运行时存储](/dotnet/core/deploying/runtime-store)中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="69b1f-412">增强型应用无需对程序集进行编译时引用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="69b1f-413">构建承载启动后，使用清单项目文件和 [dotnet store](/dotnet/core/tools/dotnet-store) 命令生成运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="69b1f-414">在示例应用（ *RuntimeStore* 项目）中，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="69b1f-414">In the sample app ( *RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="69b1f-415">对于发现运行时存储的运行时，运行时存储的位置将添加到 `DOTNET_SHARED_STORE` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="69b1f-416">**修改并放置承载启动的依赖项文件**</span><span class="sxs-lookup"><span data-stu-id="69b1f-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="69b1f-417">要在未包引用增强功能的情况下激活增强功能，请使用 `additionalDeps` 为运行时指定附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="69b1f-418">使用 `additionalDeps`，你可以：</span><span class="sxs-lookup"><span data-stu-id="69b1f-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="69b1f-419">通过提供一组附加的 .deps.json 文件来扩展应用的库图，以便在启动时与应用自身的 .deps.json 文件合并 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="69b1f-420">使承载启动程序集可被发现并可加载。</span><span class="sxs-lookup"><span data-stu-id="69b1f-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="69b1f-421">生成附加依赖项文件的推荐方法是：</span><span class="sxs-lookup"><span data-stu-id="69b1f-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="69b1f-422">对上一节中引用的运行时存储清单文件执行 `dotnet publish`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="69b1f-423">从库中删除清单引用，以及生成的 deps.json 文件的 `runtime` 部分。</span><span class="sxs-lookup"><span data-stu-id="69b1f-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="69b1f-424">在示例项目中，`store.manifest/1.0.0` 属性已从 `targets` 和 `libraries` 部分中删除：</span><span class="sxs-lookup"><span data-stu-id="69b1f-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="69b1f-425">将 .deps.json 文件放入以下位置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="69b1f-426">`{ADDITIONAL DEPENDENCIES PATH}`：添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量的位置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="69b1f-427">`{SHARED FRAMEWORK NAME}`：此附加依赖项文件所必需的共享框架。</span><span class="sxs-lookup"><span data-stu-id="69b1f-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="69b1f-428">`{SHARED FRAMEWORK VERSION}`：最低共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="69b1f-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="69b1f-429">`{ENHANCEMENT ASSEMBLY NAME}`：增强程序集名称。</span><span class="sxs-lookup"><span data-stu-id="69b1f-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="69b1f-430">在示例应用（ *RuntimeStore*  项目）中，附加依赖项文件放于以下位置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-430">In the sample app ( *RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="69b1f-431">对于发现运行时存储位置的运行时，附加依赖项文件位置将添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="69b1f-432">在示例应用（ *RuntimeStore* 项目）中，使用 [PowerShell](/powershell/scripting/powershell-scripting) 脚本完成构建运行时存储并生成附加依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="69b1f-432">In the sample app ( *RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="69b1f-433">有关如何设置各种操作系统的环境变量的示例，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="69b1f-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="69b1f-434">**部署**</span><span class="sxs-lookup"><span data-stu-id="69b1f-434">**Deployment**</span></span>

<span data-ttu-id="69b1f-435">为了便于在多计算机环境中部署托承载启动，示例应用在已发布的输出中创建一个“deployment”文件夹，其中包含：</span><span class="sxs-lookup"><span data-stu-id="69b1f-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="69b1f-436">承载启动运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="69b1f-437">承载启动依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="69b1f-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="69b1f-438">PowerShell 脚本，用于创建或修改 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE` 和 `DOTNET_ADDITIONAL_DEPS` 以支持激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="69b1f-439">从部署系统上的管理 PowerShell 命令提示符运行脚本。</span><span class="sxs-lookup"><span data-stu-id="69b1f-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="69b1f-440">NuGet 程序包</span><span class="sxs-lookup"><span data-stu-id="69b1f-440">NuGet package</span></span>

<span data-ttu-id="69b1f-441">可在 NuGet 包中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="69b1f-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="69b1f-442">该包含有 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="69b1f-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="69b1f-443">包提供的承载启动类型使用以下任一方法提供给应用：</span><span class="sxs-lookup"><span data-stu-id="69b1f-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="69b1f-444">增强型应用的项目文件在应用的项目文件（编译时引用）中为承载启动提供了包引用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="69b1f-445">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="69b1f-446">此方法适用于已发布到 [nuget.org](https://www.nuget.org/) 的承载启动程序集包。</span><span class="sxs-lookup"><span data-stu-id="69b1f-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="69b1f-447">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="69b1f-448">有关 NuGet 包和运行时存储的详细信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="69b1f-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="69b1f-449">如何使用跨平台工具创建 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="69b1f-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="69b1f-450">发布包</span><span class="sxs-lookup"><span data-stu-id="69b1f-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="69b1f-451">运行时包存储</span><span class="sxs-lookup"><span data-stu-id="69b1f-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="69b1f-452">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="69b1f-452">Project bin folder</span></span>

<span data-ttu-id="69b1f-453">承载启动增强功能可通过增强型应用中的 bin -deployed 程序集提供。</span><span class="sxs-lookup"><span data-stu-id="69b1f-453">A hosting startup enhancement can be provided by a *bin* -deployed assembly in the enhanced app.</span></span> <span data-ttu-id="69b1f-454">程序集提供的承载启动类型使用以下方法之一提供给应用：</span><span class="sxs-lookup"><span data-stu-id="69b1f-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="69b1f-455">增强型应用的项目文件对承载启动进行了程序集引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="69b1f-456">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file ( *.deps.json* ).</span></span> <span data-ttu-id="69b1f-457">当部署方案要求对承载启动程序集（.dll 文件）进行编译时引用并将程序集移动到以下任一位置时，此方法适用：</span><span class="sxs-lookup"><span data-stu-id="69b1f-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly ( *.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="69b1f-458">进行中的项目。</span><span class="sxs-lookup"><span data-stu-id="69b1f-458">The consuming project.</span></span>
  * <span data-ttu-id="69b1f-459">进行中的项目可访问的位置。</span><span class="sxs-lookup"><span data-stu-id="69b1f-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="69b1f-460">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="69b1f-461">在以 .NET Framework 为目标时，程序集可在默认加载上下文中加载，这在 .NET Framework 上表示程序集位于以下任一位置：</span><span class="sxs-lookup"><span data-stu-id="69b1f-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="69b1f-462">应用程序基路径：应用的可执行文件 (.exe) 所在的 bin 文件夹。</span><span class="sxs-lookup"><span data-stu-id="69b1f-462">Application base path: The *bin* folder where the app's executable ( *.exe* ) is located.</span></span>
  * <span data-ttu-id="69b1f-463">全局程序集缓存 (GAC)：GAC 存储多个 .NET Framework 应用共享的程序集。</span><span class="sxs-lookup"><span data-stu-id="69b1f-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="69b1f-464">有关详细信息，请参阅[如何：将程序集安装到 .NET Framework 文档中的全局程序集缓存](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="69b1f-465">示例代码</span><span class="sxs-lookup"><span data-stu-id="69b1f-465">Sample code</span></span>

<span data-ttu-id="69b1f-466">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）演示了承载启动实现方案：</span><span class="sxs-lookup"><span data-stu-id="69b1f-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="69b1f-467">两个承载启动程序集（类库）分别设置一对内存中配置键值对：</span><span class="sxs-lookup"><span data-stu-id="69b1f-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="69b1f-468">NuGet 包 (HostingStartupPackage)</span><span class="sxs-lookup"><span data-stu-id="69b1f-468">NuGet package ( *HostingStartupPackage* )</span></span>
  * <span data-ttu-id="69b1f-469">类库 (HostingStartupLibrary)</span><span class="sxs-lookup"><span data-stu-id="69b1f-469">Class library ( *HostingStartupLibrary* )</span></span>
* <span data-ttu-id="69b1f-470">从运行时存储部署的程序集 (StartupDiagnostics) 激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="69b1f-470">A hosting startup is activated from a runtime store-deployed assembly ( *StartupDiagnostics* ).</span></span> <span data-ttu-id="69b1f-471">该程序集在启动时将两个中间件添加到应用，用于提供以下内容的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="69b1f-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="69b1f-472">已注册服务</span><span class="sxs-lookup"><span data-stu-id="69b1f-472">Registered services</span></span>
  * <span data-ttu-id="69b1f-473">地址（方案、主机、基路径、路径、查询字符串）</span><span class="sxs-lookup"><span data-stu-id="69b1f-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="69b1f-474">连接（远程 IP、远程端口、本地 IP、本地端口、客户端证书）</span><span class="sxs-lookup"><span data-stu-id="69b1f-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="69b1f-475">请求标头</span><span class="sxs-lookup"><span data-stu-id="69b1f-475">Request headers</span></span>
  * <span data-ttu-id="69b1f-476">环境变量</span><span class="sxs-lookup"><span data-stu-id="69b1f-476">Environment variables</span></span>

<span data-ttu-id="69b1f-477">运行示例：</span><span class="sxs-lookup"><span data-stu-id="69b1f-477">To run the sample:</span></span>

<span data-ttu-id="69b1f-478">**从 NuGet 包激活**</span><span class="sxs-lookup"><span data-stu-id="69b1f-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="69b1f-479">使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令编译 HostingStartupPackage 包。</span><span class="sxs-lookup"><span data-stu-id="69b1f-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="69b1f-480">将包的程序集名称 HostingStartupPackage 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="69b1f-481">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-481">Compile and run the app.</span></span> <span data-ttu-id="69b1f-482">增强型应用中存在包引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="69b1f-483">应用项目文件中的 `<PropertyGroup>` 指定包项目的输出 (../HostingStartupPackage/bin/Debug) 作为包源。</span><span class="sxs-lookup"><span data-stu-id="69b1f-483">A `<PropertyGroup>` in the app's project file specifies the package project's output ( *../HostingStartupPackage/bin/Debug* ) as a package source.</span></span> <span data-ttu-id="69b1f-484">这允许应用使用该包而无需将包上传到 [nuget.org](https://www.nuget.org/)。有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="69b1f-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="69b1f-485">观察到索引页呈现的服务配置键值与包的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="69b1f-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="69b1f-486">如果更改并重新编译 HostingStartupPackage 项目，请清除本地 NuGet 包缓存，确保 HostingStartupApp 从本地缓存中收到更新后的包而不是旧包 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="69b1f-487">要清除本地 NuGet 缓存，请执行以下 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 命令：</span><span class="sxs-lookup"><span data-stu-id="69b1f-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="69b1f-488">**从类库激活**</span><span class="sxs-lookup"><span data-stu-id="69b1f-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="69b1f-489">使用 [dotnet build](/dotnet/core/tools/dotnet-build) 命令编译 HostingStartupLibrary 类库。</span><span class="sxs-lookup"><span data-stu-id="69b1f-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="69b1f-490">将类库的程序集名称 HostingStartupLibrary 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="69b1f-491">bin - 通过将类库编译输出中的 HostingStartupLibrary.dll 文件复制到应用的 bin/Debug 文件夹，将类库程序集部署到应用  。</span><span class="sxs-lookup"><span data-stu-id="69b1f-491">*bin* -deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="69b1f-492">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-492">Compile and run the app.</span></span> <span data-ttu-id="69b1f-493">应用项目文件中的 `<ItemGroup>` 引用类库的程序集 (.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll)（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-493">An `<ItemGroup>` in the app's project file references the class library's assembly ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (a compile-time reference).</span></span> <span data-ttu-id="69b1f-494">有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="69b1f-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="69b1f-495">观察到索引页呈现的服务配置键值与类库的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="69b1f-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="69b1f-496">**从运行时存储部署的程序集激活**</span><span class="sxs-lookup"><span data-stu-id="69b1f-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="69b1f-497">StartupDiagnostics 项目使用 [PowerShell](/powershell/scripting/powershell-scripting) 修改其 StartupDiagnostics.deps.json 文件 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="69b1f-498">默认情况下，Windows 7 SP1 和 Windows Server 2008 R2 SP1 及以后版本的 Windows 上安装有 PowerShell。</span><span class="sxs-lookup"><span data-stu-id="69b1f-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="69b1f-499">若要在其他平台上获取 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。</span><span class="sxs-lookup"><span data-stu-id="69b1f-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="69b1f-500">执行 RuntimeStore文件夹中的 build.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="69b1f-501">脚本：</span><span class="sxs-lookup"><span data-stu-id="69b1f-501">The script:</span></span>
   * <span data-ttu-id="69b1f-502">在 obj\packages 文件夹中生成 `StartupDiagnostics` 包。</span><span class="sxs-lookup"><span data-stu-id="69b1f-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="69b1f-503">在 store 文件夹中生成 `StartupDiagnostics` 的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="69b1f-504">该脚本中的 `dotnet store` 命令使用 `win7-x64` [运行时标识符 (RID)](/dotnet/core/rid-catalog) 将托管启动部署到 Windows。</span><span class="sxs-lookup"><span data-stu-id="69b1f-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="69b1f-505">为其他运行时提供托管启动程序集时，在脚本第 37 行上替换为正确的 RID。</span><span class="sxs-lookup"><span data-stu-id="69b1f-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="69b1f-506">`StartupDiagnostics` 的运行时存储稍后会移动到将使用程序集的计算机上的用户或系统的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="69b1f-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="69b1f-507">`StartupDiagnostics` 程序集的用户运行时存储安装位置是 .dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll。</span><span class="sxs-lookup"><span data-stu-id="69b1f-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="69b1f-508">在 additionalDeps 文件夹中生成 `StartupDiagnostics` 的 `additionalDeps`。</span><span class="sxs-lookup"><span data-stu-id="69b1f-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="69b1f-509">附加依赖项稍后会移动到用户或系统的附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="69b1f-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="69b1f-510">用户 `StartupDiagnostics` 附加依赖项安装位置是 .dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json。</span><span class="sxs-lookup"><span data-stu-id="69b1f-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="69b1f-511">将 deploy.ps1 文件放置在 deployment 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="69b1f-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="69b1f-512">运行 deployment 文件夹中的 deploy.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="69b1f-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="69b1f-513">脚本将：</span><span class="sxs-lookup"><span data-stu-id="69b1f-513">The script appends:</span></span>
   * <span data-ttu-id="69b1f-514">`StartupDiagnostics` 追加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="69b1f-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="69b1f-515">`DOTNET_ADDITIONAL_DEPS` 环境变量的承载启动依赖项路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="69b1f-516">`DOTNET_SHARED_STORE` 环境变量的运行时存储路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="69b1f-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="69b1f-517">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="69b1f-517">Run the sample app.</span></span>
1. <span data-ttu-id="69b1f-518">请求 `/services` 终结点以查看应用的注册服务。</span><span class="sxs-lookup"><span data-stu-id="69b1f-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="69b1f-519">请求 `/diag` 终结点以查看诊断信息。</span><span class="sxs-lookup"><span data-stu-id="69b1f-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
