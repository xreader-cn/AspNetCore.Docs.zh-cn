---
<span data-ttu-id="e072b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e072b-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e072b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e072b-102">'Blazor'</span></span>
- <span data-ttu-id="e072b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e072b-103">'Identity'</span></span>
- <span data-ttu-id="e072b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e072b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e072b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e072b-105">'Razor'</span></span>
- <span data-ttu-id="e072b-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e072b-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="e072b-107">在 ASP.NET Core 中使用承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="e072b-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="e072b-108">作者：[Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="e072b-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e072b-109">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>（承载启动）实现，在启动时从外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="e072b-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e072b-110">例如，外部库可使用承载启动实现为应用提供其他配置提供程序或服务。</span><span class="sxs-lookup"><span data-stu-id="e072b-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e072b-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e072b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e072b-112">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="e072b-112">HostingStartup attribute</span></span>

<span data-ttu-id="e072b-113">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性表示存在要在运行时激活的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e072b-114">将自动扫描输入程序集或包含 `Startup` 类的程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-115">用于搜索 `HostingStartup` 属性的程序集列表在运行时从 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 中的配置加载。</span><span class="sxs-lookup"><span data-stu-id="e072b-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e072b-116">要从发现中排除的程序集列表从 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) 加载。</span><span class="sxs-lookup"><span data-stu-id="e072b-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="e072b-117">在以下示例中，承载启动程序集的命名空间为 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="e072b-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e072b-118">包含承载启动代码的类是 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="e072b-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e072b-119">`HostingStartup` 属性通常位于承载启动程序集的 `IHostingStartup` 实现类文件中。</span><span class="sxs-lookup"><span data-stu-id="e072b-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e072b-120">发现加载的承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="e072b-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e072b-121">要发现加载的承载启动程序集，请启用日志记录并检查应用的日志。</span><span class="sxs-lookup"><span data-stu-id="e072b-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e072b-122">记录加载程序集时发生的错误。</span><span class="sxs-lookup"><span data-stu-id="e072b-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e072b-123">会在调试级别记录加载的承载启动程序集，并记录所有错误。</span><span class="sxs-lookup"><span data-stu-id="e072b-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e072b-124">禁用承载启动程序集的自动加载</span><span class="sxs-lookup"><span data-stu-id="e072b-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e072b-125">要禁用承载启动程序集的自动加载，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="e072b-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e072b-126">要阻止加载所有承载启动程序集，请将以下内容之一设置为 `true` 或 `1`：</span><span class="sxs-lookup"><span data-stu-id="e072b-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="e072b-127">阻止承载启动主机配置设置：</span><span class="sxs-lookup"><span data-stu-id="e072b-127">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="e072b-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="e072b-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="e072b-129">要阻止加载特定的承载启动程序集，请将以下之一设置为以分号分隔的承载启动程序集字符串，以便在启动时排除：</span><span class="sxs-lookup"><span data-stu-id="e072b-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="e072b-130">承载启动排除程序集承载配置设置：</span><span class="sxs-lookup"><span data-stu-id="e072b-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="e072b-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="e072b-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e072b-132">如果同时设置了主机配置设置和环境变量，则主机设置将控制行为。</span><span class="sxs-lookup"><span data-stu-id="e072b-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e072b-133">如果使用主机设置或环境变量来禁用承载启动程序集，将全局禁用程序集，并可能会禁用应用的多个特征。</span><span class="sxs-lookup"><span data-stu-id="e072b-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e072b-134">项目</span><span class="sxs-lookup"><span data-stu-id="e072b-134">Project</span></span>

<span data-ttu-id="e072b-135">使用以下任一项目类型创建承载启动：</span><span class="sxs-lookup"><span data-stu-id="e072b-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e072b-136">类库</span><span class="sxs-lookup"><span data-stu-id="e072b-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="e072b-137">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="e072b-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e072b-138">类库</span><span class="sxs-lookup"><span data-stu-id="e072b-138">Class library</span></span>

<span data-ttu-id="e072b-139">可在类库中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="e072b-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e072b-140">库包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e072b-141">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)包括 Razor Pages 应用、HostingStartupApp 和类库 HostingStartupLibrary 。</span><span class="sxs-lookup"><span data-stu-id="e072b-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e072b-142">类库：</span><span class="sxs-lookup"><span data-stu-id="e072b-142">The class library:</span></span>

* <span data-ttu-id="e072b-143">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="e072b-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e072b-144">`ServiceKeyInjection` 使用内存中配置提供程序 ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="e072b-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e072b-145">包含 `HostingStartup` 属性，用于标识承载启动的命名空间和类。</span><span class="sxs-lookup"><span data-stu-id="e072b-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e072b-146">`ServiceKeyInjection` 类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="e072b-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e072b-147">HostingStartupLibrary/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e072b-148">应用的索引页读取并呈现类库承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="e072b-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e072b-149">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e072b-150">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)还包括一个 NuGet 包项目，该项目提供单独的承载启动 HostingStartupPackage。</span><span class="sxs-lookup"><span data-stu-id="e072b-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e072b-151">该包具有与前述类库相同的特征。</span><span class="sxs-lookup"><span data-stu-id="e072b-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e072b-152">包：</span><span class="sxs-lookup"><span data-stu-id="e072b-152">The package:</span></span>

* <span data-ttu-id="e072b-153">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="e072b-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e072b-154">`ServiceKeyInjection` 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="e072b-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e072b-155">包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e072b-156">HostingStartupPackage/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e072b-157">应用的索引页读取并呈现包承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="e072b-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e072b-158">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e072b-159">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="e072b-159">Console app without an entry point</span></span>

<span data-ttu-id="e072b-160">此方法仅适用于 .NET Core 应用，不适用于 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e072b-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e072b-161">可在包含 `HostingStartup` 属性的无入口点的控制台应用中提供动态承载启动增强功能，该功能无需编译时引用进行激活。</span><span class="sxs-lookup"><span data-stu-id="e072b-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-162">发布控制台应用会生成可从运行时存储中使用的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e072b-163">此过程中使用没有入口点的控制台应用，因为：</span><span class="sxs-lookup"><span data-stu-id="e072b-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e072b-164">需要依赖项文件来使用承载启动程序集中的承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e072b-165">依赖项文件是一种可运行的应用资产，它通过发布应用而不是库来生成。</span><span class="sxs-lookup"><span data-stu-id="e072b-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e072b-166">无法直接将库添加到[运行时包存储](/dotnet/core/deploying/runtime-store)，该过程需要一个以已共享运行时为目标的可运行项目。</span><span class="sxs-lookup"><span data-stu-id="e072b-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e072b-167">创建动态承载启动过程中：</span><span class="sxs-lookup"><span data-stu-id="e072b-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e072b-168">从控制台应用创建承载启动程序集，无需以下入口点：</span><span class="sxs-lookup"><span data-stu-id="e072b-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e072b-169">包含 `IHostingStartup` 实现的类。</span><span class="sxs-lookup"><span data-stu-id="e072b-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e072b-170">包含用于识别 `IHostingStartup` 实现类的 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e072b-171">发布控制台应用，获取承载启动的依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e072b-172">发布控制台应用的结果是从依赖项文件中删除了未使用的依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e072b-173">修改依赖项文件以设置承载启动程序集的运行时位置。</span><span class="sxs-lookup"><span data-stu-id="e072b-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e072b-174">承载启动程序集及其依赖项文件位于运行时包存储中。</span><span class="sxs-lookup"><span data-stu-id="e072b-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e072b-175">要发现承载启动程序集及其依赖项文件，它们将在一对环境变量中列出。</span><span class="sxs-lookup"><span data-stu-id="e072b-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e072b-176">控制台应用引用 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 包：</span><span class="sxs-lookup"><span data-stu-id="e072b-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="e072b-177">生成 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 时，[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性将类标识为 `IHostingStartup` 的实现，用于加载和执行。</span><span class="sxs-lookup"><span data-stu-id="e072b-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e072b-178">在下面的示例中，命名空间为 `StartupEnhancement`，类为 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="e072b-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e072b-179">类实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="e072b-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e072b-180">该类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="e072b-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e072b-181">用户代码中 `Startup.Configure` 之前的运行时调用托管启动程序集中的 `IHostingStartup.Configure`，允许用户代码覆盖托管启动程序集提供的任何配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e072b-182">生成 `IHostingStartup` 项目时，依赖项文件 (.deps.json) 将程序集的 `runtime` 位置设为 bin 文件夹 ：</span><span class="sxs-lookup"><span data-stu-id="e072b-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e072b-183">仅显示部分文件。</span><span class="sxs-lookup"><span data-stu-id="e072b-183">Only part of the file is shown.</span></span> <span data-ttu-id="e072b-184">示例中程序集的名称是 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="e072b-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e072b-185">托管启动提供的配置</span><span class="sxs-lookup"><span data-stu-id="e072b-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e072b-186">处理配置有两种方法，具体取决于是希望托管启动的配置优先还是应用的配置优先：</span><span class="sxs-lookup"><span data-stu-id="e072b-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e072b-187">使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行后加载配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e072b-188">使用此方法，托管启动配置优先于应用的配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e072b-189">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行之前加载配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e072b-190">使用此方法，应用的配置值优先于托管启动程序提供的值。</span><span class="sxs-lookup"><span data-stu-id="e072b-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e072b-191">指定承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="e072b-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e072b-192">对于类库或控制台应用提供的承载启动，请在 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中指定承载启动程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="e072b-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e072b-193">环境变量是以分号分隔的程序集列表。</span><span class="sxs-lookup"><span data-stu-id="e072b-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e072b-194">仅扫描承载启动程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-195">对于示例应用 HostingStartupApp，要发现前述的承载启动，请将环境变量设置为以下值：</span><span class="sxs-lookup"><span data-stu-id="e072b-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e072b-196">还可使用承载启动程序集主机配置设置来设置此承载启动程序集：</span><span class="sxs-lookup"><span data-stu-id="e072b-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="e072b-197">存在多个托管启动程序集时，将按列出程序集的顺序执行 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法。</span><span class="sxs-lookup"><span data-stu-id="e072b-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e072b-198">激活</span><span class="sxs-lookup"><span data-stu-id="e072b-198">Activation</span></span>

<span data-ttu-id="e072b-199">承载启动激活的选项包括：</span><span class="sxs-lookup"><span data-stu-id="e072b-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e072b-200">[运行时存储](#runtime-store)：激活无需用于激活的编译时引用。</span><span class="sxs-lookup"><span data-stu-id="e072b-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e072b-201">示例应用将承载启动程序集和依赖项文件放入文件夹“deployment”，以便在多计算机环境中部署承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e072b-202">“deployment”文件夹还包括 PowerShell 脚本，该脚本可在部署系统上创建或修改环境变量以启用承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e072b-203">激活所需的编译时引用</span><span class="sxs-lookup"><span data-stu-id="e072b-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e072b-204">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="e072b-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e072b-205">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="e072b-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e072b-206">运行时存储</span><span class="sxs-lookup"><span data-stu-id="e072b-206">Runtime store</span></span>

<span data-ttu-id="e072b-207">承载启动实现位于[运行时存储](/dotnet/core/deploying/runtime-store)中。</span><span class="sxs-lookup"><span data-stu-id="e072b-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e072b-208">增强型应用无需对程序集进行编译时引用。</span><span class="sxs-lookup"><span data-stu-id="e072b-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e072b-209">构建承载启动后，使用清单项目文件和 [dotnet store](/dotnet/core/tools/dotnet-store) 命令生成运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e072b-210">在示例应用（*RuntimeStore* 项目）中，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="e072b-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e072b-211">对于发现运行时存储的运行时，运行时存储的位置将添加到 `DOTNET_SHARED_STORE` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e072b-212">**修改并放置承载启动的依赖项文件**</span><span class="sxs-lookup"><span data-stu-id="e072b-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e072b-213">要在未包引用增强功能的情况下激活增强功能，请使用 `additionalDeps` 为运行时指定附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e072b-214">使用 `additionalDeps`，你可以：</span><span class="sxs-lookup"><span data-stu-id="e072b-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e072b-215">通过提供一组附加的 .deps.json 文件来扩展应用的库图，以便在启动时与应用自身的 .deps.json 文件合并 。</span><span class="sxs-lookup"><span data-stu-id="e072b-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e072b-216">使承载启动程序集可被发现并可加载。</span><span class="sxs-lookup"><span data-stu-id="e072b-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e072b-217">生成附加依赖项文件的推荐方法是：</span><span class="sxs-lookup"><span data-stu-id="e072b-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e072b-218">对上一节中引用的运行时存储清单文件执行 `dotnet publish`。</span><span class="sxs-lookup"><span data-stu-id="e072b-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e072b-219">从库中删除清单引用，以及生成的 deps.json 文件的 `runtime` 部分。</span><span class="sxs-lookup"><span data-stu-id="e072b-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e072b-220">在示例项目中，`store.manifest/1.0.0` 属性已从 `targets` 和 `libraries` 部分中删除：</span><span class="sxs-lookup"><span data-stu-id="e072b-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="e072b-221">将 .deps.json 文件放入以下位置：</span><span class="sxs-lookup"><span data-stu-id="e072b-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e072b-222">`{ADDITIONAL DEPENDENCIES PATH}`：添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量的位置。</span><span class="sxs-lookup"><span data-stu-id="e072b-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e072b-223">`{SHARED FRAMEWORK NAME}`：此附加依赖项文件所必需的共享框架。</span><span class="sxs-lookup"><span data-stu-id="e072b-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e072b-224">`{SHARED FRAMEWORK VERSION}`：最低共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="e072b-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e072b-225">`{ENHANCEMENT ASSEMBLY NAME}`：增强程序集名称。</span><span class="sxs-lookup"><span data-stu-id="e072b-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e072b-226">在示例应用（*RuntimeStore*  项目）中，附加依赖项文件放于以下位置：</span><span class="sxs-lookup"><span data-stu-id="e072b-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e072b-227">对于发现运行时存储位置的运行时，附加依赖项文件位置将添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e072b-228">在示例应用（*RuntimeStore* 项目）中，使用 [PowerShell](/powershell/scripting/powershell-scripting) 脚本完成构建运行时存储并生成附加依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="e072b-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e072b-229">有关如何设置各种操作系统的环境变量的示例，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="e072b-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e072b-230">**部署**</span><span class="sxs-lookup"><span data-stu-id="e072b-230">**Deployment**</span></span>

<span data-ttu-id="e072b-231">为了便于在多计算机环境中部署托承载启动，示例应用在已发布的输出中创建一个“deployment”文件夹，其中包含：</span><span class="sxs-lookup"><span data-stu-id="e072b-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e072b-232">承载启动运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e072b-233">承载启动依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="e072b-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e072b-234">PowerShell 脚本，用于创建或修改 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE` 和 `DOTNET_ADDITIONAL_DEPS` 以支持激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e072b-235">从部署系统上的管理 PowerShell 命令提示符运行脚本。</span><span class="sxs-lookup"><span data-stu-id="e072b-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e072b-236">NuGet 程序包</span><span class="sxs-lookup"><span data-stu-id="e072b-236">NuGet package</span></span>

<span data-ttu-id="e072b-237">可在 NuGet 包中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="e072b-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e072b-238">该包含有 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-239">包提供的承载启动类型使用以下任一方法提供给应用：</span><span class="sxs-lookup"><span data-stu-id="e072b-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e072b-240">增强型应用的项目文件在应用的项目文件（编译时引用）中为承载启动提供了包引用。</span><span class="sxs-lookup"><span data-stu-id="e072b-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e072b-241">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="e072b-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e072b-242">此方法适用于已发布到 [nuget.org](https://www.nuget.org/) 的承载启动程序集包。</span><span class="sxs-lookup"><span data-stu-id="e072b-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e072b-243">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e072b-244">有关 NuGet 包和运行时存储的详细信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e072b-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e072b-245">如何使用跨平台工具创建 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="e072b-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e072b-246">发布包</span><span class="sxs-lookup"><span data-stu-id="e072b-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e072b-247">运行时包存储</span><span class="sxs-lookup"><span data-stu-id="e072b-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e072b-248">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="e072b-248">Project bin folder</span></span>

<span data-ttu-id="e072b-249">承载启动增强功能可通过增强型应用中的 bin -deployed 程序集提供。</span><span class="sxs-lookup"><span data-stu-id="e072b-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e072b-250">程序集提供的承载启动类型使用以下方法之一提供给应用：</span><span class="sxs-lookup"><span data-stu-id="e072b-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e072b-251">增强型应用的项目文件对承载启动进行了程序集引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e072b-252">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="e072b-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e072b-253">当部署方案要求对承载启动程序集（.dll 文件）进行编译时引用并将程序集移动到以下任一位置时，此方法适用：</span><span class="sxs-lookup"><span data-stu-id="e072b-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e072b-254">进行中的项目。</span><span class="sxs-lookup"><span data-stu-id="e072b-254">The consuming project.</span></span>
  * <span data-ttu-id="e072b-255">进行中的项目可访问的位置。</span><span class="sxs-lookup"><span data-stu-id="e072b-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e072b-256">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e072b-257">在以 .NET Framework 为目标时，程序集可在默认加载上下文中加载，这在 .NET Framework 上表示程序集位于以下任一位置：</span><span class="sxs-lookup"><span data-stu-id="e072b-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e072b-258">应用程序基路径：应用的可执行文件 (.exe) 所在的 bin 文件夹。</span><span class="sxs-lookup"><span data-stu-id="e072b-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e072b-259">全局程序集缓存 (GAC)：GAC 存储多个 .NET Framework 应用共享的程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e072b-260">有关详细信息，请参阅[如何：将程序集安装到 .NET Framework 文档中的全局程序集缓存](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)中。</span><span class="sxs-lookup"><span data-stu-id="e072b-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e072b-261">示例代码</span><span class="sxs-lookup"><span data-stu-id="e072b-261">Sample code</span></span>

<span data-ttu-id="e072b-262">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）演示了承载启动实现方案：</span><span class="sxs-lookup"><span data-stu-id="e072b-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e072b-263">两个承载启动程序集（类库）分别设置一对内存中配置键值对：</span><span class="sxs-lookup"><span data-stu-id="e072b-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e072b-264">NuGet 包 (HostingStartupPackage)</span><span class="sxs-lookup"><span data-stu-id="e072b-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e072b-265">类库 (HostingStartupLibrary)</span><span class="sxs-lookup"><span data-stu-id="e072b-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e072b-266">从运行时存储部署的程序集 (StartupDiagnostics) 激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e072b-267">该程序集在启动时将两个中间件添加到应用，用于提供以下内容的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="e072b-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e072b-268">已注册服务</span><span class="sxs-lookup"><span data-stu-id="e072b-268">Registered services</span></span>
  * <span data-ttu-id="e072b-269">地址（方案、主机、基路径、路径、查询字符串）</span><span class="sxs-lookup"><span data-stu-id="e072b-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e072b-270">连接（远程 IP、远程端口、本地 IP、本地端口、客户端证书）</span><span class="sxs-lookup"><span data-stu-id="e072b-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e072b-271">请求标头</span><span class="sxs-lookup"><span data-stu-id="e072b-271">Request headers</span></span>
  * <span data-ttu-id="e072b-272">环境变量</span><span class="sxs-lookup"><span data-stu-id="e072b-272">Environment variables</span></span>

<span data-ttu-id="e072b-273">运行示例：</span><span class="sxs-lookup"><span data-stu-id="e072b-273">To run the sample:</span></span>

<span data-ttu-id="e072b-274">**从 NuGet 包激活**</span><span class="sxs-lookup"><span data-stu-id="e072b-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e072b-275">使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令编译 HostingStartupPackage 包。</span><span class="sxs-lookup"><span data-stu-id="e072b-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e072b-276">将包的程序集名称 HostingStartupPackage 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e072b-277">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e072b-277">Compile and run the app.</span></span> <span data-ttu-id="e072b-278">增强型应用中存在包引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e072b-279">应用项目文件中的 `<PropertyGroup>` 指定包项目的输出 (../HostingStartupPackage/bin/Debug) 作为包源。</span><span class="sxs-lookup"><span data-stu-id="e072b-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e072b-280">这允许应用使用该包而无需将包上传到 [nuget.org](https://www.nuget.org/)。有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="e072b-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e072b-281">观察到索引页呈现的服务配置键值与包的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="e072b-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e072b-282">如果更改并重新编译 HostingStartupPackage 项目，请清除本地 NuGet 包缓存，确保 HostingStartupApp 从本地缓存中收到更新后的包而不是旧包 。</span><span class="sxs-lookup"><span data-stu-id="e072b-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e072b-283">要清除本地 NuGet 缓存，请执行以下 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 命令：</span><span class="sxs-lookup"><span data-stu-id="e072b-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e072b-284">**从类库激活**</span><span class="sxs-lookup"><span data-stu-id="e072b-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="e072b-285">使用 [dotnet build](/dotnet/core/tools/dotnet-build) 命令编译 HostingStartupLibrary 类库。</span><span class="sxs-lookup"><span data-stu-id="e072b-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e072b-286">将类库的程序集名称 HostingStartupLibrary 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e072b-287">bin - 通过将类库编译输出中的 HostingStartupLibrary.dll 文件复制到应用的 bin/Debug 文件夹，将类库程序集部署到应用  。</span><span class="sxs-lookup"><span data-stu-id="e072b-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e072b-288">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e072b-288">Compile and run the app.</span></span> <span data-ttu-id="e072b-289">应用项目文件中的 `<ItemGroup>` 引用类库的程序集 (.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll)（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e072b-290">有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="e072b-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e072b-291">观察到索引页呈现的服务配置键值与类库的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="e072b-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e072b-292">**从运行时存储部署的程序集激活**</span><span class="sxs-lookup"><span data-stu-id="e072b-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e072b-293">StartupDiagnostics 项目使用 [PowerShell](/powershell/scripting/powershell-scripting) 修改其 StartupDiagnostics.deps.json 文件 。</span><span class="sxs-lookup"><span data-stu-id="e072b-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e072b-294">默认情况下，Windows 7 SP1 和 Windows Server 2008 R2 SP1 及以后版本的 Windows 上安装有 PowerShell。</span><span class="sxs-lookup"><span data-stu-id="e072b-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e072b-295">若要在其他平台上获取 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。</span><span class="sxs-lookup"><span data-stu-id="e072b-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e072b-296">执行 RuntimeStore文件夹中的 build.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="e072b-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e072b-297">脚本：</span><span class="sxs-lookup"><span data-stu-id="e072b-297">The script:</span></span>
   * <span data-ttu-id="e072b-298">在 obj\packages 文件夹中生成 `StartupDiagnostics` 包。</span><span class="sxs-lookup"><span data-stu-id="e072b-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e072b-299">在 store 文件夹中生成 `StartupDiagnostics` 的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e072b-300">该脚本中的 `dotnet store` 命令使用 `win7-x64` [运行时标识符 (RID)](/dotnet/core/rid-catalog) 将托管启动部署到 Windows。</span><span class="sxs-lookup"><span data-stu-id="e072b-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e072b-301">为其他运行时提供托管启动程序集时，在脚本第 37 行上替换为正确的 RID。</span><span class="sxs-lookup"><span data-stu-id="e072b-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e072b-302">`StartupDiagnostics` 的运行时存储稍后会移动到将使用程序集的计算机上的用户或系统的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e072b-303">`StartupDiagnostics` 程序集的用户运行时存储安装位置是 .dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll。</span><span class="sxs-lookup"><span data-stu-id="e072b-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e072b-304">在 additionalDeps 文件夹中生成 `StartupDiagnostics` 的 `additionalDeps`。</span><span class="sxs-lookup"><span data-stu-id="e072b-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e072b-305">附加依赖项稍后会移动到用户或系统的附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e072b-306">用户 `StartupDiagnostics` 附加依赖项安装位置是 .dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json。</span><span class="sxs-lookup"><span data-stu-id="e072b-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e072b-307">将 deploy.ps1 文件放置在 deployment 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e072b-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e072b-308">运行 deployment 文件夹中的 deploy.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="e072b-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e072b-309">脚本将：</span><span class="sxs-lookup"><span data-stu-id="e072b-309">The script appends:</span></span>
   * <span data-ttu-id="e072b-310">`StartupDiagnostics` 追加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="e072b-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e072b-311">`DOTNET_ADDITIONAL_DEPS` 环境变量的承载启动依赖项路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="e072b-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e072b-312">`DOTNET_SHARED_STORE` 环境变量的运行时存储路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="e072b-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e072b-313">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="e072b-313">Run the sample app.</span></span>
1. <span data-ttu-id="e072b-314">请求 `/services` 终结点以查看应用的注册服务。</span><span class="sxs-lookup"><span data-stu-id="e072b-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e072b-315">请求 `/diag` 终结点以查看诊断信息。</span><span class="sxs-lookup"><span data-stu-id="e072b-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e072b-316">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>（承载启动）实现，在启动时从外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="e072b-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e072b-317">例如，外部库可使用承载启动实现为应用提供其他配置提供程序或服务。</span><span class="sxs-lookup"><span data-stu-id="e072b-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e072b-318">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e072b-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e072b-319">HostingStartup 属性</span><span class="sxs-lookup"><span data-stu-id="e072b-319">HostingStartup attribute</span></span>

<span data-ttu-id="e072b-320">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性表示存在要在运行时激活的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e072b-321">将自动扫描输入程序集或包含 `Startup` 类的程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-322">用于搜索 `HostingStartup` 属性的程序集列表在运行时从 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 中的配置加载。</span><span class="sxs-lookup"><span data-stu-id="e072b-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e072b-323">要从发现中排除的程序集列表从 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) 加载。</span><span class="sxs-lookup"><span data-stu-id="e072b-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="e072b-324">有关详细信息，请参阅 [Web 主机：承载启动程序集](xref:fundamentals/host/web-host#hosting-startup-assemblies)和 [Web 主机：承载启动排除程序集](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)。</span><span class="sxs-lookup"><span data-stu-id="e072b-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="e072b-325">在以下示例中，承载启动程序集的命名空间为 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="e072b-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e072b-326">包含承载启动代码的类是 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="e072b-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e072b-327">`HostingStartup` 属性通常位于承载启动程序集的 `IHostingStartup` 实现类文件中。</span><span class="sxs-lookup"><span data-stu-id="e072b-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e072b-328">发现加载的承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="e072b-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e072b-329">要发现加载的承载启动程序集，请启用日志记录并检查应用的日志。</span><span class="sxs-lookup"><span data-stu-id="e072b-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e072b-330">记录加载程序集时发生的错误。</span><span class="sxs-lookup"><span data-stu-id="e072b-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e072b-331">会在调试级别记录加载的承载启动程序集，并记录所有错误。</span><span class="sxs-lookup"><span data-stu-id="e072b-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e072b-332">禁用承载启动程序集的自动加载</span><span class="sxs-lookup"><span data-stu-id="e072b-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e072b-333">要禁用承载启动程序集的自动加载，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="e072b-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e072b-334">要阻止加载所有承载启动程序集，请将以下内容之一设置为 `true` 或 `1`：</span><span class="sxs-lookup"><span data-stu-id="e072b-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="e072b-335">[阻止承载启动](xref:fundamentals/host/web-host#prevent-hosting-startup)主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="e072b-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="e072b-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="e072b-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="e072b-337">要阻止加载特定的承载启动程序集，请将以下之一设置为以分号分隔的承载启动程序集字符串，以便在启动时排除：</span><span class="sxs-lookup"><span data-stu-id="e072b-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="e072b-338">[承载启动排除程序集](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)承载配置设置。</span><span class="sxs-lookup"><span data-stu-id="e072b-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="e072b-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="e072b-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e072b-340">如果同时设置了主机配置设置和环境变量，则主机设置将控制行为。</span><span class="sxs-lookup"><span data-stu-id="e072b-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e072b-341">如果使用主机设置或环境变量来禁用承载启动程序集，将全局禁用程序集，并可能会禁用应用的多个特征。</span><span class="sxs-lookup"><span data-stu-id="e072b-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e072b-342">项目</span><span class="sxs-lookup"><span data-stu-id="e072b-342">Project</span></span>

<span data-ttu-id="e072b-343">使用以下任一项目类型创建承载启动：</span><span class="sxs-lookup"><span data-stu-id="e072b-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e072b-344">类库</span><span class="sxs-lookup"><span data-stu-id="e072b-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="e072b-345">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="e072b-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e072b-346">类库</span><span class="sxs-lookup"><span data-stu-id="e072b-346">Class library</span></span>

<span data-ttu-id="e072b-347">可在类库中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="e072b-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e072b-348">库包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e072b-349">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)包括 Razor Pages 应用、HostingStartupApp 和类库 HostingStartupLibrary 。</span><span class="sxs-lookup"><span data-stu-id="e072b-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e072b-350">类库：</span><span class="sxs-lookup"><span data-stu-id="e072b-350">The class library:</span></span>

* <span data-ttu-id="e072b-351">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="e072b-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e072b-352">`ServiceKeyInjection` 使用内存中配置提供程序 ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="e072b-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e072b-353">包含 `HostingStartup` 属性，用于标识承载启动的命名空间和类。</span><span class="sxs-lookup"><span data-stu-id="e072b-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e072b-354">`ServiceKeyInjection` 类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="e072b-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e072b-355">HostingStartupLibrary/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e072b-356">应用的索引页读取并呈现类库承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="e072b-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e072b-357">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e072b-358">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)还包括一个 NuGet 包项目，该项目提供单独的承载启动 HostingStartupPackage。</span><span class="sxs-lookup"><span data-stu-id="e072b-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e072b-359">该包具有与前述类库相同的特征。</span><span class="sxs-lookup"><span data-stu-id="e072b-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e072b-360">包：</span><span class="sxs-lookup"><span data-stu-id="e072b-360">The package:</span></span>

* <span data-ttu-id="e072b-361">包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="e072b-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e072b-362">`ServiceKeyInjection` 将一对服务字符串添加到应用的配置中。</span><span class="sxs-lookup"><span data-stu-id="e072b-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e072b-363">包含 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e072b-364">HostingStartupPackage/ServiceKeyInjection.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e072b-365">应用的索引页读取并呈现包承载启动程序集设置的两个键的配置值：</span><span class="sxs-lookup"><span data-stu-id="e072b-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e072b-366">HostingStartupApp/Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="e072b-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e072b-367">无入口点的控制台应用</span><span class="sxs-lookup"><span data-stu-id="e072b-367">Console app without an entry point</span></span>

<span data-ttu-id="e072b-368">此方法仅适用于 .NET Core 应用，不适用于 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e072b-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e072b-369">可在包含 `HostingStartup` 属性的无入口点的控制台应用中提供动态承载启动增强功能，该功能无需编译时引用进行激活。</span><span class="sxs-lookup"><span data-stu-id="e072b-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-370">发布控制台应用会生成可从运行时存储中使用的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e072b-371">此过程中使用没有入口点的控制台应用，因为：</span><span class="sxs-lookup"><span data-stu-id="e072b-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e072b-372">需要依赖项文件来使用承载启动程序集中的承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e072b-373">依赖项文件是一种可运行的应用资产，它通过发布应用而不是库来生成。</span><span class="sxs-lookup"><span data-stu-id="e072b-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e072b-374">无法直接将库添加到[运行时包存储](/dotnet/core/deploying/runtime-store)，该过程需要一个以已共享运行时为目标的可运行项目。</span><span class="sxs-lookup"><span data-stu-id="e072b-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e072b-375">创建动态承载启动过程中：</span><span class="sxs-lookup"><span data-stu-id="e072b-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e072b-376">从控制台应用创建承载启动程序集，无需以下入口点：</span><span class="sxs-lookup"><span data-stu-id="e072b-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e072b-377">包含 `IHostingStartup` 实现的类。</span><span class="sxs-lookup"><span data-stu-id="e072b-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e072b-378">包含用于识别 `IHostingStartup` 实现类的 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e072b-379">发布控制台应用，获取承载启动的依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e072b-380">发布控制台应用的结果是从依赖项文件中删除了未使用的依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e072b-381">修改依赖项文件以设置承载启动程序集的运行时位置。</span><span class="sxs-lookup"><span data-stu-id="e072b-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e072b-382">承载启动程序集及其依赖项文件位于运行时包存储中。</span><span class="sxs-lookup"><span data-stu-id="e072b-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e072b-383">要发现承载启动程序集及其依赖项文件，它们将在一对环境变量中列出。</span><span class="sxs-lookup"><span data-stu-id="e072b-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e072b-384">控制台应用引用 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 包：</span><span class="sxs-lookup"><span data-stu-id="e072b-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="e072b-385">生成 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 时，[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性将类标识为 `IHostingStartup` 的实现，用于加载和执行。</span><span class="sxs-lookup"><span data-stu-id="e072b-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e072b-386">在下面的示例中，命名空间为 `StartupEnhancement`，类为 `StartupEnhancementHostingStartup`：</span><span class="sxs-lookup"><span data-stu-id="e072b-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e072b-387">类实现 `IHostingStartup`。</span><span class="sxs-lookup"><span data-stu-id="e072b-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e072b-388">该类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="e072b-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e072b-389">用户代码中 `Startup.Configure` 之前的运行时调用托管启动程序集中的 `IHostingStartup.Configure`，允许用户代码覆盖托管启动程序集提供的任何配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e072b-390">生成 `IHostingStartup` 项目时，依赖项文件 (.deps.json) 将程序集的 `runtime` 位置设为 bin 文件夹 ：</span><span class="sxs-lookup"><span data-stu-id="e072b-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e072b-391">仅显示部分文件。</span><span class="sxs-lookup"><span data-stu-id="e072b-391">Only part of the file is shown.</span></span> <span data-ttu-id="e072b-392">示例中程序集的名称是 `StartupEnhancement`。</span><span class="sxs-lookup"><span data-stu-id="e072b-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e072b-393">托管启动提供的配置</span><span class="sxs-lookup"><span data-stu-id="e072b-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e072b-394">处理配置有两种方法，具体取决于是希望托管启动的配置优先还是应用的配置优先：</span><span class="sxs-lookup"><span data-stu-id="e072b-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e072b-395">使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行后加载配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e072b-396">使用此方法，托管启动配置优先于应用的配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e072b-397">使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行之前加载配置。</span><span class="sxs-lookup"><span data-stu-id="e072b-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e072b-398">使用此方法，应用的配置值优先于托管启动程序提供的值。</span><span class="sxs-lookup"><span data-stu-id="e072b-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e072b-399">指定承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="e072b-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e072b-400">对于类库或控制台应用提供的承载启动，请在 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中指定承载启动程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="e072b-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e072b-401">环境变量是以分号分隔的程序集列表。</span><span class="sxs-lookup"><span data-stu-id="e072b-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e072b-402">仅扫描承载启动程序集以查找 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-403">对于示例应用 HostingStartupApp，要发现前述的承载启动，请将环境变量设置为以下值：</span><span class="sxs-lookup"><span data-stu-id="e072b-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e072b-404">还可使用[承载启动程序集](xref:fundamentals/host/web-host#hosting-startup-assemblies)主机配置设置来设置此承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="e072b-405">存在多个托管启动程序集时，将按列出程序集的顺序执行 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法。</span><span class="sxs-lookup"><span data-stu-id="e072b-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e072b-406">激活</span><span class="sxs-lookup"><span data-stu-id="e072b-406">Activation</span></span>

<span data-ttu-id="e072b-407">承载启动激活的选项包括：</span><span class="sxs-lookup"><span data-stu-id="e072b-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e072b-408">[运行时存储](#runtime-store)：激活无需用于激活的编译时引用。</span><span class="sxs-lookup"><span data-stu-id="e072b-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e072b-409">示例应用将承载启动程序集和依赖项文件放入文件夹“deployment”，以便在多计算机环境中部署承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e072b-410">“deployment”文件夹还包括 PowerShell 脚本，该脚本可在部署系统上创建或修改环境变量以启用承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e072b-411">激活所需的编译时引用</span><span class="sxs-lookup"><span data-stu-id="e072b-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e072b-412">NuGet 包</span><span class="sxs-lookup"><span data-stu-id="e072b-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e072b-413">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="e072b-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e072b-414">运行时存储</span><span class="sxs-lookup"><span data-stu-id="e072b-414">Runtime store</span></span>

<span data-ttu-id="e072b-415">承载启动实现位于[运行时存储](/dotnet/core/deploying/runtime-store)中。</span><span class="sxs-lookup"><span data-stu-id="e072b-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e072b-416">增强型应用无需对程序集进行编译时引用。</span><span class="sxs-lookup"><span data-stu-id="e072b-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e072b-417">构建承载启动后，使用清单项目文件和 [dotnet store](/dotnet/core/tools/dotnet-store) 命令生成运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e072b-418">在示例应用（*RuntimeStore* 项目）中，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="e072b-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e072b-419">对于发现运行时存储的运行时，运行时存储的位置将添加到 `DOTNET_SHARED_STORE` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e072b-420">**修改并放置承载启动的依赖项文件**</span><span class="sxs-lookup"><span data-stu-id="e072b-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e072b-421">要在未包引用增强功能的情况下激活增强功能，请使用 `additionalDeps` 为运行时指定附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e072b-422">使用 `additionalDeps`，你可以：</span><span class="sxs-lookup"><span data-stu-id="e072b-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e072b-423">通过提供一组附加的 .deps.json 文件来扩展应用的库图，以便在启动时与应用自身的 .deps.json 文件合并 。</span><span class="sxs-lookup"><span data-stu-id="e072b-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e072b-424">使承载启动程序集可被发现并可加载。</span><span class="sxs-lookup"><span data-stu-id="e072b-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e072b-425">生成附加依赖项文件的推荐方法是：</span><span class="sxs-lookup"><span data-stu-id="e072b-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e072b-426">对上一节中引用的运行时存储清单文件执行 `dotnet publish`。</span><span class="sxs-lookup"><span data-stu-id="e072b-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e072b-427">从库中删除清单引用，以及生成的 deps.json 文件的 `runtime` 部分。</span><span class="sxs-lookup"><span data-stu-id="e072b-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e072b-428">在示例项目中，`store.manifest/1.0.0` 属性已从 `targets` 和 `libraries` 部分中删除：</span><span class="sxs-lookup"><span data-stu-id="e072b-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="e072b-429">将 .deps.json 文件放入以下位置：</span><span class="sxs-lookup"><span data-stu-id="e072b-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e072b-430">`{ADDITIONAL DEPENDENCIES PATH}`：添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量的位置。</span><span class="sxs-lookup"><span data-stu-id="e072b-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e072b-431">`{SHARED FRAMEWORK NAME}`：此附加依赖项文件所必需的共享框架。</span><span class="sxs-lookup"><span data-stu-id="e072b-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e072b-432">`{SHARED FRAMEWORK VERSION}`：最低共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="e072b-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e072b-433">`{ENHANCEMENT ASSEMBLY NAME}`：增强程序集名称。</span><span class="sxs-lookup"><span data-stu-id="e072b-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e072b-434">在示例应用（*RuntimeStore*  项目）中，附加依赖项文件放于以下位置：</span><span class="sxs-lookup"><span data-stu-id="e072b-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e072b-435">对于发现运行时存储位置的运行时，附加依赖项文件位置将添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e072b-436">在示例应用（*RuntimeStore* 项目）中，使用 [PowerShell](/powershell/scripting/powershell-scripting) 脚本完成构建运行时存储并生成附加依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="e072b-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e072b-437">有关如何设置各种操作系统的环境变量的示例，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="e072b-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e072b-438">**部署**</span><span class="sxs-lookup"><span data-stu-id="e072b-438">**Deployment**</span></span>

<span data-ttu-id="e072b-439">为了便于在多计算机环境中部署托承载启动，示例应用在已发布的输出中创建一个“deployment”文件夹，其中包含：</span><span class="sxs-lookup"><span data-stu-id="e072b-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e072b-440">承载启动运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e072b-441">承载启动依赖项文件。</span><span class="sxs-lookup"><span data-stu-id="e072b-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e072b-442">PowerShell 脚本，用于创建或修改 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE` 和 `DOTNET_ADDITIONAL_DEPS` 以支持激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e072b-443">从部署系统上的管理 PowerShell 命令提示符运行脚本。</span><span class="sxs-lookup"><span data-stu-id="e072b-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e072b-444">NuGet 程序包</span><span class="sxs-lookup"><span data-stu-id="e072b-444">NuGet package</span></span>

<span data-ttu-id="e072b-445">可在 NuGet 包中提供承载启动增强。</span><span class="sxs-lookup"><span data-stu-id="e072b-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e072b-446">该包含有 `HostingStartup` 属性。</span><span class="sxs-lookup"><span data-stu-id="e072b-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e072b-447">包提供的承载启动类型使用以下任一方法提供给应用：</span><span class="sxs-lookup"><span data-stu-id="e072b-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e072b-448">增强型应用的项目文件在应用的项目文件（编译时引用）中为承载启动提供了包引用。</span><span class="sxs-lookup"><span data-stu-id="e072b-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e072b-449">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="e072b-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e072b-450">此方法适用于已发布到 [nuget.org](https://www.nuget.org/) 的承载启动程序集包。</span><span class="sxs-lookup"><span data-stu-id="e072b-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e072b-451">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e072b-452">有关 NuGet 包和运行时存储的详细信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e072b-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e072b-453">如何使用跨平台工具创建 NuGet 包</span><span class="sxs-lookup"><span data-stu-id="e072b-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e072b-454">发布包</span><span class="sxs-lookup"><span data-stu-id="e072b-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e072b-455">运行时包存储</span><span class="sxs-lookup"><span data-stu-id="e072b-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e072b-456">项目 bin 文件夹</span><span class="sxs-lookup"><span data-stu-id="e072b-456">Project bin folder</span></span>

<span data-ttu-id="e072b-457">承载启动增强功能可通过增强型应用中的 bin -deployed 程序集提供。</span><span class="sxs-lookup"><span data-stu-id="e072b-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e072b-458">程序集提供的承载启动类型使用以下方法之一提供给应用：</span><span class="sxs-lookup"><span data-stu-id="e072b-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e072b-459">增强型应用的项目文件对承载启动进行了程序集引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e072b-460">有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。</span><span class="sxs-lookup"><span data-stu-id="e072b-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e072b-461">当部署方案要求对承载启动程序集（.dll 文件）进行编译时引用并将程序集移动到以下任一位置时，此方法适用：</span><span class="sxs-lookup"><span data-stu-id="e072b-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e072b-462">进行中的项目。</span><span class="sxs-lookup"><span data-stu-id="e072b-462">The consuming project.</span></span>
  * <span data-ttu-id="e072b-463">进行中的项目可访问的位置。</span><span class="sxs-lookup"><span data-stu-id="e072b-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e072b-464">承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e072b-465">在以 .NET Framework 为目标时，程序集可在默认加载上下文中加载，这在 .NET Framework 上表示程序集位于以下任一位置：</span><span class="sxs-lookup"><span data-stu-id="e072b-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e072b-466">应用程序基路径：应用的可执行文件 (.exe) 所在的 bin 文件夹。</span><span class="sxs-lookup"><span data-stu-id="e072b-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e072b-467">全局程序集缓存 (GAC)：GAC 存储多个 .NET Framework 应用共享的程序集。</span><span class="sxs-lookup"><span data-stu-id="e072b-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e072b-468">有关详细信息，请参阅[如何：将程序集安装到 .NET Framework 文档中的全局程序集缓存](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)中。</span><span class="sxs-lookup"><span data-stu-id="e072b-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e072b-469">示例代码</span><span class="sxs-lookup"><span data-stu-id="e072b-469">Sample code</span></span>

<span data-ttu-id="e072b-470">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）演示了承载启动实现方案：</span><span class="sxs-lookup"><span data-stu-id="e072b-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e072b-471">两个承载启动程序集（类库）分别设置一对内存中配置键值对：</span><span class="sxs-lookup"><span data-stu-id="e072b-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e072b-472">NuGet 包 (HostingStartupPackage)</span><span class="sxs-lookup"><span data-stu-id="e072b-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e072b-473">类库 (HostingStartupLibrary)</span><span class="sxs-lookup"><span data-stu-id="e072b-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e072b-474">从运行时存储部署的程序集 (StartupDiagnostics) 激活承载启动。</span><span class="sxs-lookup"><span data-stu-id="e072b-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e072b-475">该程序集在启动时将两个中间件添加到应用，用于提供以下内容的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="e072b-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e072b-476">已注册服务</span><span class="sxs-lookup"><span data-stu-id="e072b-476">Registered services</span></span>
  * <span data-ttu-id="e072b-477">地址（方案、主机、基路径、路径、查询字符串）</span><span class="sxs-lookup"><span data-stu-id="e072b-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e072b-478">连接（远程 IP、远程端口、本地 IP、本地端口、客户端证书）</span><span class="sxs-lookup"><span data-stu-id="e072b-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e072b-479">请求标头</span><span class="sxs-lookup"><span data-stu-id="e072b-479">Request headers</span></span>
  * <span data-ttu-id="e072b-480">环境变量</span><span class="sxs-lookup"><span data-stu-id="e072b-480">Environment variables</span></span>

<span data-ttu-id="e072b-481">运行示例：</span><span class="sxs-lookup"><span data-stu-id="e072b-481">To run the sample:</span></span>

<span data-ttu-id="e072b-482">**从 NuGet 包激活**</span><span class="sxs-lookup"><span data-stu-id="e072b-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e072b-483">使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令编译 HostingStartupPackage 包。</span><span class="sxs-lookup"><span data-stu-id="e072b-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e072b-484">将包的程序集名称 HostingStartupPackage 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e072b-485">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e072b-485">Compile and run the app.</span></span> <span data-ttu-id="e072b-486">增强型应用中存在包引用（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e072b-487">应用项目文件中的 `<PropertyGroup>` 指定包项目的输出 (../HostingStartupPackage/bin/Debug) 作为包源。</span><span class="sxs-lookup"><span data-stu-id="e072b-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e072b-488">这允许应用使用该包而无需将包上传到 [nuget.org](https://www.nuget.org/)。有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="e072b-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e072b-489">观察到索引页呈现的服务配置键值与包的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="e072b-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e072b-490">如果更改并重新编译 HostingStartupPackage 项目，请清除本地 NuGet 包缓存，确保 HostingStartupApp 从本地缓存中收到更新后的包而不是旧包 。</span><span class="sxs-lookup"><span data-stu-id="e072b-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e072b-491">要清除本地 NuGet 缓存，请执行以下 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 命令：</span><span class="sxs-lookup"><span data-stu-id="e072b-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e072b-492">**从类库激活**</span><span class="sxs-lookup"><span data-stu-id="e072b-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="e072b-493">使用 [dotnet build](/dotnet/core/tools/dotnet-build) 命令编译 HostingStartupLibrary 类库。</span><span class="sxs-lookup"><span data-stu-id="e072b-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e072b-494">将类库的程序集名称 HostingStartupLibrary 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="e072b-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e072b-495">bin - 通过将类库编译输出中的 HostingStartupLibrary.dll 文件复制到应用的 bin/Debug 文件夹，将类库程序集部署到应用  。</span><span class="sxs-lookup"><span data-stu-id="e072b-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e072b-496">编译并运行应用。</span><span class="sxs-lookup"><span data-stu-id="e072b-496">Compile and run the app.</span></span> <span data-ttu-id="e072b-497">应用项目文件中的 `<ItemGroup>` 引用类库的程序集 (.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll)（编译时引用）。</span><span class="sxs-lookup"><span data-stu-id="e072b-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e072b-498">有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。</span><span class="sxs-lookup"><span data-stu-id="e072b-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e072b-499">观察到索引页呈现的服务配置键值与类库的 `ServiceKeyInjection.Configure` 方法设置的值匹配。</span><span class="sxs-lookup"><span data-stu-id="e072b-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e072b-500">**从运行时存储部署的程序集激活**</span><span class="sxs-lookup"><span data-stu-id="e072b-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e072b-501">StartupDiagnostics 项目使用 [PowerShell](/powershell/scripting/powershell-scripting) 修改其 StartupDiagnostics.deps.json 文件 。</span><span class="sxs-lookup"><span data-stu-id="e072b-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e072b-502">默认情况下，Windows 7 SP1 和 Windows Server 2008 R2 SP1 及以后版本的 Windows 上安装有 PowerShell。</span><span class="sxs-lookup"><span data-stu-id="e072b-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e072b-503">若要在其他平台上获取 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。</span><span class="sxs-lookup"><span data-stu-id="e072b-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e072b-504">执行 RuntimeStore文件夹中的 build.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="e072b-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e072b-505">脚本：</span><span class="sxs-lookup"><span data-stu-id="e072b-505">The script:</span></span>
   * <span data-ttu-id="e072b-506">在 obj\packages 文件夹中生成 `StartupDiagnostics` 包。</span><span class="sxs-lookup"><span data-stu-id="e072b-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e072b-507">在 store 文件夹中生成 `StartupDiagnostics` 的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e072b-508">该脚本中的 `dotnet store` 命令使用 `win7-x64` [运行时标识符 (RID)](/dotnet/core/rid-catalog) 将托管启动部署到 Windows。</span><span class="sxs-lookup"><span data-stu-id="e072b-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e072b-509">为其他运行时提供托管启动程序集时，在脚本第 37 行上替换为正确的 RID。</span><span class="sxs-lookup"><span data-stu-id="e072b-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e072b-510">`StartupDiagnostics` 的运行时存储稍后会移动到将使用程序集的计算机上的用户或系统的运行时存储。</span><span class="sxs-lookup"><span data-stu-id="e072b-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e072b-511">`StartupDiagnostics` 程序集的用户运行时存储安装位置是 .dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll。</span><span class="sxs-lookup"><span data-stu-id="e072b-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e072b-512">在 additionalDeps 文件夹中生成 `StartupDiagnostics` 的 `additionalDeps`。</span><span class="sxs-lookup"><span data-stu-id="e072b-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e072b-513">附加依赖项稍后会移动到用户或系统的附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="e072b-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e072b-514">用户 `StartupDiagnostics` 附加依赖项安装位置是 .dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json。</span><span class="sxs-lookup"><span data-stu-id="e072b-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e072b-515">将 deploy.ps1 文件放置在 deployment 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="e072b-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e072b-516">运行 deployment 文件夹中的 deploy.ps1 脚本 。</span><span class="sxs-lookup"><span data-stu-id="e072b-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e072b-517">脚本将：</span><span class="sxs-lookup"><span data-stu-id="e072b-517">The script appends:</span></span>
   * <span data-ttu-id="e072b-518">`StartupDiagnostics` 追加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="e072b-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e072b-519">`DOTNET_ADDITIONAL_DEPS` 环境变量的承载启动依赖项路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="e072b-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e072b-520">`DOTNET_SHARED_STORE` 环境变量的运行时存储路径（在 RuntimeStore 项目的 deployment 文件夹中）。</span><span class="sxs-lookup"><span data-stu-id="e072b-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e072b-521">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="e072b-521">Run the sample app.</span></span>
1. <span data-ttu-id="e072b-522">请求 `/services` 终结点以查看应用的注册服务。</span><span class="sxs-lookup"><span data-stu-id="e072b-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e072b-523">请求 `/diag` 终结点以查看诊断信息。</span><span class="sxs-lookup"><span data-stu-id="e072b-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
