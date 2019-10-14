---
title: ASP.NET Core 中的文件提供程序
author: guardrex
description: 了解 ASP.NET Core 如何通过文件提供程序来抽象化文件系统访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 3a92b44efc70d156596ee9fe80b4f6a65266e73d
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007169"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="bda8b-103">ASP.NET Core 中的文件提供程序</span><span class="sxs-lookup"><span data-stu-id="bda8b-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="bda8b-104">作者：[Steve Smith](https://ardalis.com/) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bda8b-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bda8b-105">ASP.NET Core 通过文件提供程序来抽象化文件系统访问。</span><span class="sxs-lookup"><span data-stu-id="bda8b-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="bda8b-106">在 ASP.NET Core 框架中使用文件提供程序：</span><span class="sxs-lookup"><span data-stu-id="bda8b-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="bda8b-107">`IWebHostEnvironment` 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。</span><span class="sxs-lookup"><span data-stu-id="bda8b-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="bda8b-108">[静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="bda8b-109">[Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。</span><span class="sxs-lookup"><span data-stu-id="bda8b-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="bda8b-110">.NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="bda8b-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bda8b-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="bda8b-112">文件提供程序接口</span><span class="sxs-lookup"><span data-stu-id="bda8b-112">File Provider interfaces</span></span>

<span data-ttu-id="bda8b-113">主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="bda8b-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="bda8b-114">`IFileProvider` 公开方法以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="bda8b-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="bda8b-115">获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="bda8b-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="bda8b-116">获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="bda8b-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="bda8b-117">设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。</span><span class="sxs-lookup"><span data-stu-id="bda8b-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="bda8b-118">`IFileInfo` 提供用于处理文件的方法和属性：</span><span class="sxs-lookup"><span data-stu-id="bda8b-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="bda8b-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）</span><span class="sxs-lookup"><span data-stu-id="bda8b-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="bda8b-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期</span><span class="sxs-lookup"><span data-stu-id="bda8b-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="bda8b-121">可以使用 [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 方法从文件读取内容。</span><span class="sxs-lookup"><span data-stu-id="bda8b-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="bda8b-122">示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖关系注入](xref:fundamentals/dependency-injection)在应用中使用。</span><span class="sxs-lookup"><span data-stu-id="bda8b-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="bda8b-123">文件提供程序实现</span><span class="sxs-lookup"><span data-stu-id="bda8b-123">File Provider implementations</span></span>

<span data-ttu-id="bda8b-124">`IFileProvider` 有三种实现。</span><span class="sxs-lookup"><span data-stu-id="bda8b-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="bda8b-125">实现</span><span class="sxs-lookup"><span data-stu-id="bda8b-125">Implementation</span></span> | <span data-ttu-id="bda8b-126">说明</span><span class="sxs-lookup"><span data-stu-id="bda8b-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="bda8b-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="bda8b-128">物理提供程序用于访问系统的物理文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="bda8b-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="bda8b-130">清单嵌入式提供程序用于访问程序集中嵌入的文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="bda8b-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="bda8b-132">复合式提供程序提供对一个或多个其他提供程序中的文件和目录的合并访问。</span><span class="sxs-lookup"><span data-stu-id="bda8b-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="bda8b-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-133">PhysicalFileProvider</span></span>

<span data-ttu-id="bda8b-134"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。</span><span class="sxs-lookup"><span data-stu-id="bda8b-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="bda8b-135">`PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。</span><span class="sxs-lookup"><span data-stu-id="bda8b-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="bda8b-136">此范围界定可防止访问指定目录和子目录之外的文件系统。</span><span class="sxs-lookup"><span data-stu-id="bda8b-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="bda8b-137">创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="bda8b-138">直接实例化此提供程序时，必须提供目录路径并将其作为使用提供程序发出的所有请求的基路径。</span><span class="sxs-lookup"><span data-stu-id="bda8b-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="bda8b-139">下面的代码演示如何创建 `PhysicalFileProvider` 并用它来获取目录内容和文件信息：</span><span class="sxs-lookup"><span data-stu-id="bda8b-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="bda8b-140">前面的示例中的类型：</span><span class="sxs-lookup"><span data-stu-id="bda8b-140">Types in the preceding example:</span></span>

* <span data-ttu-id="bda8b-141">`provider` 是 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="bda8b-142">`contents` 是 `IDirectoryContents`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="bda8b-143">`fileInfo` 是 `IFileInfo`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="bda8b-144">文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。</span><span class="sxs-lookup"><span data-stu-id="bda8b-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="bda8b-145">该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bda8b-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="bda8b-146">示例应用使用 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) 在应用的 `Startup.ConfigureServices` 类中创建提供程序：</span><span class="sxs-lookup"><span data-stu-id="bda8b-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="bda8b-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="bda8b-148"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="bda8b-149">`ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。</span><span class="sxs-lookup"><span data-stu-id="bda8b-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="bda8b-150">将包引用添加到 [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) 包的项目。</span><span class="sxs-lookup"><span data-stu-id="bda8b-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="bda8b-151">若要生成嵌入的文件清单，请将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="bda8b-152">指定要使用 [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：</span><span class="sxs-lookup"><span data-stu-id="bda8b-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="bda8b-153">使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="bda8b-154">示例应用创建 `ManifestEmbeddedFileProvider` 并将当前正在执行的程序集传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="bda8b-155">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="bda8b-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="bda8b-156">其他重载使你能够：</span><span class="sxs-lookup"><span data-stu-id="bda8b-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="bda8b-157">指定相对文件路径。</span><span class="sxs-lookup"><span data-stu-id="bda8b-157">Specify a relative file path.</span></span>
* <span data-ttu-id="bda8b-158">将文件范围限制到上次修改日期。</span><span class="sxs-lookup"><span data-stu-id="bda8b-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="bda8b-159">为包含嵌入文件清单的嵌入资源命名。</span><span class="sxs-lookup"><span data-stu-id="bda8b-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="bda8b-160">重载</span><span class="sxs-lookup"><span data-stu-id="bda8b-160">Overload</span></span> | <span data-ttu-id="bda8b-161">说明</span><span class="sxs-lookup"><span data-stu-id="bda8b-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="bda8b-162">接受一个可选的 `root` 相对路径参数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="bda8b-163">指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。</span><span class="sxs-lookup"><span data-stu-id="bda8b-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="bda8b-164">接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="bda8b-165">`lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。</span><span class="sxs-lookup"><span data-stu-id="bda8b-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="bda8b-166">接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="bda8b-167">`manifestName` 表示包含清单的嵌入资源的名称。</span><span class="sxs-lookup"><span data-stu-id="bda8b-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="bda8b-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-168">CompositeFileProvider</span></span>

<span data-ttu-id="bda8b-169"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="bda8b-170">创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="bda8b-171">在示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件：</span><span class="sxs-lookup"><span data-stu-id="bda8b-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="bda8b-172">监视更改</span><span class="sxs-lookup"><span data-stu-id="bda8b-172">Watch for changes</span></span>

<span data-ttu-id="bda8b-173">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 方法提供了一个方案来监视一个或多个文件或目录是否发生更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="bda8b-174">`Watch` 接受路径字符串，它可以使用 [glob 模式](#glob-patterns)指定多个文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="bda8b-175">`Watch` 返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。</span><span class="sxs-lookup"><span data-stu-id="bda8b-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="bda8b-176">更改令牌会公开以下内容：</span><span class="sxs-lookup"><span data-stu-id="bda8b-176">The change token exposes:</span></span>

* <span data-ttu-id="bda8b-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; 可检查此属性以确定是否已发生更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="bda8b-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 检测到指定的路径字符串发生更改时调用此属性。</span><span class="sxs-lookup"><span data-stu-id="bda8b-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="bda8b-179">每个更改令牌仅调用其关联的回调来响应单个更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="bda8b-180">若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="bda8b-181">在示例应用中，WatchConsole  控制台应用配置为每次修改了文本文件时显示一条消息：</span><span class="sxs-lookup"><span data-stu-id="bda8b-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="bda8b-182">某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。</span><span class="sxs-lookup"><span data-stu-id="bda8b-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="bda8b-183">将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。</span><span class="sxs-lookup"><span data-stu-id="bda8b-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="bda8b-184">glob 模式</span><span class="sxs-lookup"><span data-stu-id="bda8b-184">Glob patterns</span></span>

<span data-ttu-id="bda8b-185">文件系统路径使用名为 glob（或通配）模式  的通配符模式。</span><span class="sxs-lookup"><span data-stu-id="bda8b-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="bda8b-186">使用这些模式指定文件的组。</span><span class="sxs-lookup"><span data-stu-id="bda8b-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="bda8b-187">两个通配符分别是 `*` 和 `**`：</span><span class="sxs-lookup"><span data-stu-id="bda8b-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="bda8b-188">匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="bda8b-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="bda8b-189">匹配由文件路径中的 `/` 和 `.` 字符终止。</span><span class="sxs-lookup"><span data-stu-id="bda8b-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="bda8b-190">匹配多个目录级别的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bda8b-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="bda8b-191">可用于以递归方式匹配目录层次结构中的许多文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="bda8b-192">**glob 模式示例**</span><span class="sxs-lookup"><span data-stu-id="bda8b-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="bda8b-193">匹配特定目录中的特定文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="bda8b-194">匹配特定目录中带 .txt  扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="bda8b-195">匹配正好位于“目录”  文件夹中下一级目录中的所有 `appsettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="bda8b-196">匹配在“目录”  文件夹下任何位置找到的带 .txt  扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bda8b-197">ASP.NET Core 通过文件提供程序来抽象化文件系统访问。</span><span class="sxs-lookup"><span data-stu-id="bda8b-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="bda8b-198">在 ASP.NET Core 框架中使用文件提供程序：</span><span class="sxs-lookup"><span data-stu-id="bda8b-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="bda8b-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。</span><span class="sxs-lookup"><span data-stu-id="bda8b-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="bda8b-200">[静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="bda8b-201">[Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。</span><span class="sxs-lookup"><span data-stu-id="bda8b-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="bda8b-202">.NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="bda8b-203">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bda8b-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="bda8b-204">文件提供程序接口</span><span class="sxs-lookup"><span data-stu-id="bda8b-204">File Provider interfaces</span></span>

<span data-ttu-id="bda8b-205">主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="bda8b-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="bda8b-206">`IFileProvider` 公开方法以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="bda8b-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="bda8b-207">获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="bda8b-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="bda8b-208">获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="bda8b-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="bda8b-209">设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。</span><span class="sxs-lookup"><span data-stu-id="bda8b-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="bda8b-210">`IFileInfo` 提供用于处理文件的方法和属性：</span><span class="sxs-lookup"><span data-stu-id="bda8b-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="bda8b-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）</span><span class="sxs-lookup"><span data-stu-id="bda8b-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="bda8b-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期</span><span class="sxs-lookup"><span data-stu-id="bda8b-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="bda8b-213">可以使用 [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 方法从文件读取内容。</span><span class="sxs-lookup"><span data-stu-id="bda8b-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="bda8b-214">示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖关系注入](xref:fundamentals/dependency-injection)在应用中使用。</span><span class="sxs-lookup"><span data-stu-id="bda8b-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="bda8b-215">文件提供程序实现</span><span class="sxs-lookup"><span data-stu-id="bda8b-215">File Provider implementations</span></span>

<span data-ttu-id="bda8b-216">`IFileProvider` 有三种实现。</span><span class="sxs-lookup"><span data-stu-id="bda8b-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="bda8b-217">实现</span><span class="sxs-lookup"><span data-stu-id="bda8b-217">Implementation</span></span> | <span data-ttu-id="bda8b-218">说明</span><span class="sxs-lookup"><span data-stu-id="bda8b-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="bda8b-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="bda8b-220">物理提供程序用于访问系统的物理文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="bda8b-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="bda8b-222">清单嵌入式提供程序用于访问程序集中嵌入的文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="bda8b-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="bda8b-224">复合式提供程序提供对一个或多个其他提供程序中的文件和目录的合并访问。</span><span class="sxs-lookup"><span data-stu-id="bda8b-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="bda8b-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-225">PhysicalFileProvider</span></span>

<span data-ttu-id="bda8b-226"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。</span><span class="sxs-lookup"><span data-stu-id="bda8b-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="bda8b-227">`PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。</span><span class="sxs-lookup"><span data-stu-id="bda8b-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="bda8b-228">此范围界定可防止访问指定目录和子目录之外的文件系统。</span><span class="sxs-lookup"><span data-stu-id="bda8b-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="bda8b-229">创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="bda8b-230">直接实例化此提供程序时，必须提供目录路径并将其作为使用提供程序发出的所有请求的基路径。</span><span class="sxs-lookup"><span data-stu-id="bda8b-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="bda8b-231">下面的代码演示如何创建 `PhysicalFileProvider` 并用它来获取目录内容和文件信息：</span><span class="sxs-lookup"><span data-stu-id="bda8b-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="bda8b-232">前面的示例中的类型：</span><span class="sxs-lookup"><span data-stu-id="bda8b-232">Types in the preceding example:</span></span>

* <span data-ttu-id="bda8b-233">`provider` 是 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="bda8b-234">`contents` 是 `IDirectoryContents`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="bda8b-235">`fileInfo` 是 `IFileInfo`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="bda8b-236">文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。</span><span class="sxs-lookup"><span data-stu-id="bda8b-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="bda8b-237">该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bda8b-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="bda8b-238">示例应用使用 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) 在应用的 `Startup.ConfigureServices` 类中创建提供程序：</span><span class="sxs-lookup"><span data-stu-id="bda8b-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="bda8b-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="bda8b-240"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="bda8b-241">`ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。</span><span class="sxs-lookup"><span data-stu-id="bda8b-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="bda8b-242">若要生成嵌入的文件清单，请将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="bda8b-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="bda8b-243">指定要使用 [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：</span><span class="sxs-lookup"><span data-stu-id="bda8b-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="bda8b-244">使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="bda8b-245">示例应用创建 `ManifestEmbeddedFileProvider` 并将当前正在执行的程序集传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="bda8b-246">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="bda8b-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="bda8b-247">其他重载使你能够：</span><span class="sxs-lookup"><span data-stu-id="bda8b-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="bda8b-248">指定相对文件路径。</span><span class="sxs-lookup"><span data-stu-id="bda8b-248">Specify a relative file path.</span></span>
* <span data-ttu-id="bda8b-249">将文件范围限制到上次修改日期。</span><span class="sxs-lookup"><span data-stu-id="bda8b-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="bda8b-250">为包含嵌入文件清单的嵌入资源命名。</span><span class="sxs-lookup"><span data-stu-id="bda8b-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="bda8b-251">重载</span><span class="sxs-lookup"><span data-stu-id="bda8b-251">Overload</span></span> | <span data-ttu-id="bda8b-252">说明</span><span class="sxs-lookup"><span data-stu-id="bda8b-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="bda8b-253">接受一个可选的 `root` 相对路径参数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="bda8b-254">指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。</span><span class="sxs-lookup"><span data-stu-id="bda8b-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="bda8b-255">接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="bda8b-256">`lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。</span><span class="sxs-lookup"><span data-stu-id="bda8b-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="bda8b-257">接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="bda8b-258">`manifestName` 表示包含清单的嵌入资源的名称。</span><span class="sxs-lookup"><span data-stu-id="bda8b-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="bda8b-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="bda8b-259">CompositeFileProvider</span></span>

<span data-ttu-id="bda8b-260"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="bda8b-261">创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bda8b-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="bda8b-262">在示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件：</span><span class="sxs-lookup"><span data-stu-id="bda8b-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="bda8b-263">监视更改</span><span class="sxs-lookup"><span data-stu-id="bda8b-263">Watch for changes</span></span>

<span data-ttu-id="bda8b-264">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 方法提供了一个方案来监视一个或多个文件或目录是否发生更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="bda8b-265">`Watch` 接受路径字符串，它可以使用 [glob 模式](#glob-patterns)指定多个文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="bda8b-266">`Watch` 返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。</span><span class="sxs-lookup"><span data-stu-id="bda8b-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="bda8b-267">更改令牌会公开以下内容：</span><span class="sxs-lookup"><span data-stu-id="bda8b-267">The change token exposes:</span></span>

* <span data-ttu-id="bda8b-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; 可检查此属性以确定是否已发生更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="bda8b-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 检测到指定的路径字符串发生更改时调用此属性。</span><span class="sxs-lookup"><span data-stu-id="bda8b-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="bda8b-270">每个更改令牌仅调用其关联的回调来响应单个更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="bda8b-271">若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。</span><span class="sxs-lookup"><span data-stu-id="bda8b-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="bda8b-272">在示例应用中，WatchConsole  控制台应用配置为每次修改了文本文件时显示一条消息：</span><span class="sxs-lookup"><span data-stu-id="bda8b-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="bda8b-273">某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。</span><span class="sxs-lookup"><span data-stu-id="bda8b-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="bda8b-274">将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。</span><span class="sxs-lookup"><span data-stu-id="bda8b-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="bda8b-275">glob 模式</span><span class="sxs-lookup"><span data-stu-id="bda8b-275">Glob patterns</span></span>

<span data-ttu-id="bda8b-276">文件系统路径使用名为 glob（或通配）模式  的通配符模式。</span><span class="sxs-lookup"><span data-stu-id="bda8b-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="bda8b-277">使用这些模式指定文件的组。</span><span class="sxs-lookup"><span data-stu-id="bda8b-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="bda8b-278">两个通配符分别是 `*` 和 `**`：</span><span class="sxs-lookup"><span data-stu-id="bda8b-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="bda8b-279">匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="bda8b-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="bda8b-280">匹配由文件路径中的 `/` 和 `.` 字符终止。</span><span class="sxs-lookup"><span data-stu-id="bda8b-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="bda8b-281">匹配多个目录级别的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bda8b-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="bda8b-282">可用于以递归方式匹配目录层次结构中的许多文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="bda8b-283">**glob 模式示例**</span><span class="sxs-lookup"><span data-stu-id="bda8b-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="bda8b-284">匹配特定目录中的特定文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="bda8b-285">匹配特定目录中带 .txt  扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="bda8b-286">匹配正好位于“目录”  文件夹中下一级目录中的所有 `appsettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="bda8b-287">匹配在“目录”  文件夹下任何位置找到的带 .txt  扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="bda8b-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
