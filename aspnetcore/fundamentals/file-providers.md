---
<span data-ttu-id="bad64-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-102">'Blazor'</span></span>
- <span data-ttu-id="bad64-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-103">'Identity'</span></span>
- <span data-ttu-id="bad64-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-105">'Razor'</span></span>
- <span data-ttu-id="bad64-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="bad64-107">ASP.NET Core 中的文件提供程序</span><span class="sxs-lookup"><span data-stu-id="bad64-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="bad64-108">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bad64-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bad64-109">ASP.NET Core 通过文件提供程序来抽象化文件系统访问。</span><span class="sxs-lookup"><span data-stu-id="bad64-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="bad64-110">在 ASP.NET Core 框架中使用文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="bad64-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="bad64-111">例如：</span><span class="sxs-lookup"><span data-stu-id="bad64-111">For example:</span></span>

* <span data-ttu-id="bad64-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。</span><span class="sxs-lookup"><span data-stu-id="bad64-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="bad64-113">[静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="bad64-114">[Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。</span><span class="sxs-lookup"><span data-stu-id="bad64-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="bad64-115">.NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="bad64-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bad64-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="bad64-117">文件提供程序接口</span><span class="sxs-lookup"><span data-stu-id="bad64-117">File Provider interfaces</span></span>

<span data-ttu-id="bad64-118">主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="bad64-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="bad64-119">`IFileProvider` 公开方法以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="bad64-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="bad64-120">获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="bad64-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="bad64-121">获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="bad64-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="bad64-122">设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。</span><span class="sxs-lookup"><span data-stu-id="bad64-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="bad64-123">`IFileInfo` 提供用于处理文件的方法和属性：</span><span class="sxs-lookup"><span data-stu-id="bad64-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="bad64-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）</span><span class="sxs-lookup"><span data-stu-id="bad64-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="bad64-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期</span><span class="sxs-lookup"><span data-stu-id="bad64-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="bad64-126">可以使用 <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> 方法从该文件读取数据。</span><span class="sxs-lookup"><span data-stu-id="bad64-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="bad64-127">FileProviderSample 示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖项注入](xref:fundamentals/dependency-injection)在应用中使用。</span><span class="sxs-lookup"><span data-stu-id="bad64-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="bad64-128">文件提供程序实现</span><span class="sxs-lookup"><span data-stu-id="bad64-128">File Provider implementations</span></span>

<span data-ttu-id="bad64-129">下表列出了 `IFileProvider` 的实现。</span><span class="sxs-lookup"><span data-stu-id="bad64-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="bad64-130">实现</span><span class="sxs-lookup"><span data-stu-id="bad64-130">Implementation</span></span> | <span data-ttu-id="bad64-131">描述</span><span class="sxs-lookup"><span data-stu-id="bad64-131">Description</span></span> |
| ---
<span data-ttu-id="bad64-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-133">'Blazor'</span></span>
- <span data-ttu-id="bad64-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-134">'Identity'</span></span>
- <span data-ttu-id="bad64-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-136">'Razor'</span></span>
- <span data-ttu-id="bad64-137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-139">'Blazor'</span></span>
- <span data-ttu-id="bad64-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-140">'Identity'</span></span>
- <span data-ttu-id="bad64-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-142">'Razor'</span></span>
- <span data-ttu-id="bad64-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-145">'Blazor'</span></span>
- <span data-ttu-id="bad64-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-146">'Identity'</span></span>
- <span data-ttu-id="bad64-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-148">'Razor'</span></span>
- <span data-ttu-id="bad64-149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-151">'Blazor'</span></span>
- <span data-ttu-id="bad64-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-152">'Identity'</span></span>
- <span data-ttu-id="bad64-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-154">'Razor'</span></span>
- <span data-ttu-id="bad64-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-157">'Blazor'</span></span>
- <span data-ttu-id="bad64-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-158">'Identity'</span></span>
- <span data-ttu-id="bad64-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-160">'Razor'</span></span>
- <span data-ttu-id="bad64-161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-161">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-163">'Blazor'</span></span>
- <span data-ttu-id="bad64-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-164">'Identity'</span></span>
- <span data-ttu-id="bad64-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-166">'Razor'</span></span>
- <span data-ttu-id="bad64-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-169">'Blazor'</span></span>
- <span data-ttu-id="bad64-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-170">'Identity'</span></span>
- <span data-ttu-id="bad64-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-172">'Razor'</span></span>
- <span data-ttu-id="bad64-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-175">'Blazor'</span></span>
- <span data-ttu-id="bad64-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-176">'Identity'</span></span>
- <span data-ttu-id="bad64-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-178">'Razor'</span></span>
- <span data-ttu-id="bad64-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-179">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-180">------ | | [CompositeFileProvider](#compositefileprovider) | 用于提供对一个或多个其他提供程序中的文件和目录的合并访问。</span><span class="sxs-lookup"><span data-stu-id="bad64-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="bad64-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 用于访问程序集内嵌入的文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="bad64-182">| | [PhysicalFileProvider](#physicalfileprovider) | 用于访问系统的物理文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="bad64-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="bad64-183">PhysicalFileProvider</span></span>

<span data-ttu-id="bad64-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。</span><span class="sxs-lookup"><span data-stu-id="bad64-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="bad64-185">`PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。</span><span class="sxs-lookup"><span data-stu-id="bad64-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="bad64-186">此范围界定可防止访问指定目录和子目录之外的文件系统。</span><span class="sxs-lookup"><span data-stu-id="bad64-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="bad64-187">创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bad64-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="bad64-188">直接实例化此提供程序时，必须提供绝对目录路径并将其作为使用提供程序发出的所有请求的基路径。</span><span class="sxs-lookup"><span data-stu-id="bad64-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="bad64-189">此目录路径中不支持 glob 模式。</span><span class="sxs-lookup"><span data-stu-id="bad64-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="bad64-190">以下代码演示如何使用 `PhysicalFileProvider` 来获取目录内容和文件信息：</span><span class="sxs-lookup"><span data-stu-id="bad64-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="bad64-191">前面的示例中的类型：</span><span class="sxs-lookup"><span data-stu-id="bad64-191">Types in the preceding example:</span></span>

* <span data-ttu-id="bad64-192">`provider` 是 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bad64-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="bad64-193">`contents` 是 `IDirectoryContents`。</span><span class="sxs-lookup"><span data-stu-id="bad64-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="bad64-194">`fileInfo` 是 `IFileInfo`。</span><span class="sxs-lookup"><span data-stu-id="bad64-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="bad64-195">文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。</span><span class="sxs-lookup"><span data-stu-id="bad64-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="bad64-196">glob 模式无法传递给 `GetFileInfo` 方法。</span><span class="sxs-lookup"><span data-stu-id="bad64-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="bad64-197">该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bad64-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="bad64-198">FileProviderSample 示例应用使用 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> 在 `Startup.ConfigureServices` 方法中创建该提供程序：</span><span class="sxs-lookup"><span data-stu-id="bad64-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="bad64-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="bad64-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="bad64-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="bad64-201">`ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。</span><span class="sxs-lookup"><span data-stu-id="bad64-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="bad64-202">若要生成嵌入文件的清单，请按照以下步骤操作：</span><span class="sxs-lookup"><span data-stu-id="bad64-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="bad64-203">将 [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet 包添加到项目中。</span><span class="sxs-lookup"><span data-stu-id="bad64-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="bad64-204">将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="bad64-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="bad64-205">指定要使用 [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：</span><span class="sxs-lookup"><span data-stu-id="bad64-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="bad64-206">使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="bad64-207">FileProviderSample 示例应用创建 `ManifestEmbeddedFileProvider`，并将当前正在执行的程序集传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bad64-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="bad64-208">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="bad64-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="bad64-209">其他重载使你能够：</span><span class="sxs-lookup"><span data-stu-id="bad64-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="bad64-210">指定相对文件路径。</span><span class="sxs-lookup"><span data-stu-id="bad64-210">Specify a relative file path.</span></span>
* <span data-ttu-id="bad64-211">将文件范围限制到上次修改日期。</span><span class="sxs-lookup"><span data-stu-id="bad64-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="bad64-212">为包含嵌入文件清单的嵌入资源命名。</span><span class="sxs-lookup"><span data-stu-id="bad64-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="bad64-213">重载</span><span class="sxs-lookup"><span data-stu-id="bad64-213">Overload</span></span> | <span data-ttu-id="bad64-214">描述</span><span class="sxs-lookup"><span data-stu-id="bad64-214">Description</span></span> |
| ---
<span data-ttu-id="bad64-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-216">'Blazor'</span></span>
- <span data-ttu-id="bad64-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-217">'Identity'</span></span>
- <span data-ttu-id="bad64-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-219">'Razor'</span></span>
- <span data-ttu-id="bad64-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-222">'Blazor'</span></span>
- <span data-ttu-id="bad64-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-223">'Identity'</span></span>
- <span data-ttu-id="bad64-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-225">'Razor'</span></span>
- <span data-ttu-id="bad64-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-226">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-228">'Blazor'</span></span>
- <span data-ttu-id="bad64-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-229">'Identity'</span></span>
- <span data-ttu-id="bad64-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-231">'Razor'</span></span>
- <span data-ttu-id="bad64-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-234">'Blazor'</span></span>
- <span data-ttu-id="bad64-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-235">'Identity'</span></span>
- <span data-ttu-id="bad64-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-237">'Razor'</span></span>
- <span data-ttu-id="bad64-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-240">'Blazor'</span></span>
- <span data-ttu-id="bad64-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-241">'Identity'</span></span>
- <span data-ttu-id="bad64-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-243">'Razor'</span></span>
- <span data-ttu-id="bad64-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-244">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | 接受一个可选的 `root` 相对路径参数。</span><span class="sxs-lookup"><span data-stu-id="bad64-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="bad64-246">指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。</span><span class="sxs-lookup"><span data-stu-id="bad64-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="bad64-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。</span><span class="sxs-lookup"><span data-stu-id="bad64-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="bad64-248">`lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。</span><span class="sxs-lookup"><span data-stu-id="bad64-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="bad64-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。</span><span class="sxs-lookup"><span data-stu-id="bad64-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="bad64-250">`manifestName` 表示包含清单的嵌入资源的名称。</span><span class="sxs-lookup"><span data-stu-id="bad64-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="bad64-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="bad64-251">CompositeFileProvider</span></span>

<span data-ttu-id="bad64-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="bad64-253">创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bad64-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="bad64-254">在 FileProviderSample 示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="bad64-255">以下代码可在项目的 `Startup.ConfigureServices` 方法中找到：</span><span class="sxs-lookup"><span data-stu-id="bad64-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="bad64-256">监视更改</span><span class="sxs-lookup"><span data-stu-id="bad64-256">Watch for changes</span></span>

<span data-ttu-id="bad64-257"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> 方法提供一种方法来监视一个或多个文件或目录的更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="bad64-258">`Watch` 方法：</span><span class="sxs-lookup"><span data-stu-id="bad64-258">The `Watch` method:</span></span>

* <span data-ttu-id="bad64-259">接受文件路径字符串（可以使用 [glob 模式](#glob-patterns)指定多个文件）。</span><span class="sxs-lookup"><span data-stu-id="bad64-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="bad64-260">返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。</span><span class="sxs-lookup"><span data-stu-id="bad64-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="bad64-261">生成的更改令牌会公开以下内容：</span><span class="sxs-lookup"><span data-stu-id="bad64-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="bad64-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>：可检查此属性以确定是否已发生更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="bad64-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>：检测到指定的路径字符串发生更改时调用此属性。</span><span class="sxs-lookup"><span data-stu-id="bad64-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="bad64-264">每个更改令牌仅调用其关联的回调来响应单个更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="bad64-265">若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="bad64-266">每当 TextFiles 目录中的 .txt 文件被修改时，WatchConsole 示例应用都会写入一条消息  ：</span><span class="sxs-lookup"><span data-stu-id="bad64-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="bad64-267">某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。</span><span class="sxs-lookup"><span data-stu-id="bad64-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="bad64-268">将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。</span><span class="sxs-lookup"><span data-stu-id="bad64-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="bad64-269">glob 模式</span><span class="sxs-lookup"><span data-stu-id="bad64-269">Glob patterns</span></span>

<span data-ttu-id="bad64-270">文件系统路径使用名为 glob（或通配）模式的通配符模式。</span><span class="sxs-lookup"><span data-stu-id="bad64-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="bad64-271">使用这些模式指定文件的组。</span><span class="sxs-lookup"><span data-stu-id="bad64-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="bad64-272">两个通配符分别是 `*` 和 `**`：</span><span class="sxs-lookup"><span data-stu-id="bad64-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="bad64-273">匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="bad64-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="bad64-274">匹配由文件路径中的 `/` 和 `.` 字符终止。</span><span class="sxs-lookup"><span data-stu-id="bad64-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="bad64-275">匹配多个目录级别的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bad64-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="bad64-276">可用于以递归方式匹配目录层次结构中的许多文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="bad64-277">下表提供了 glob 模式的常见示例。</span><span class="sxs-lookup"><span data-stu-id="bad64-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="bad64-278">模式</span><span class="sxs-lookup"><span data-stu-id="bad64-278">Pattern</span></span>  |<span data-ttu-id="bad64-279">描述</span><span class="sxs-lookup"><span data-stu-id="bad64-279">Description</span></span>  |
|---
<span data-ttu-id="bad64-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-281">'Blazor'</span></span>
- <span data-ttu-id="bad64-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-282">'Identity'</span></span>
- <span data-ttu-id="bad64-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-284">'Razor'</span></span>
- <span data-ttu-id="bad64-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-287">'Blazor'</span></span>
- <span data-ttu-id="bad64-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-288">'Identity'</span></span>
- <span data-ttu-id="bad64-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-290">'Razor'</span></span>
- <span data-ttu-id="bad64-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-291">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-293">'Blazor'</span></span>
- <span data-ttu-id="bad64-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-294">'Identity'</span></span>
- <span data-ttu-id="bad64-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-296">'Razor'</span></span>
- <span data-ttu-id="bad64-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-299">'Blazor'</span></span>
- <span data-ttu-id="bad64-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-300">'Identity'</span></span>
- <span data-ttu-id="bad64-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-302">'Razor'</span></span>
- <span data-ttu-id="bad64-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-303">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-304">-----|
|`directory/file.txt`| 匹配特定目录中的特定文件。| |`directory/*.txt`| 匹配特定目录中以 .txt\*\* 为扩展名的所有文件。| |`directory/*/appsettings.json`| 匹配目录\*\* 文件夹下一级的目录中的所有 appsettings.json\*\* 文件。| |`directory/**/*.txt`| 配置目录\*\* 文件夹下的任何位置中以 .txt\*\* 为扩展名的所有文件。|</span><span class="sxs-lookup"><span data-stu-id="bad64-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bad64-305">ASP.NET Core 通过文件提供程序来抽象化文件系统访问。</span><span class="sxs-lookup"><span data-stu-id="bad64-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="bad64-306">在 ASP.NET Core 框架中使用文件提供程序：</span><span class="sxs-lookup"><span data-stu-id="bad64-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="bad64-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。</span><span class="sxs-lookup"><span data-stu-id="bad64-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="bad64-308">[静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="bad64-309">[Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。</span><span class="sxs-lookup"><span data-stu-id="bad64-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="bad64-310">.NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="bad64-311">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="bad64-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="bad64-312">文件提供程序接口</span><span class="sxs-lookup"><span data-stu-id="bad64-312">File Provider interfaces</span></span>

<span data-ttu-id="bad64-313">主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="bad64-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="bad64-314">`IFileProvider` 公开方法以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="bad64-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="bad64-315">获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="bad64-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="bad64-316">获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="bad64-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="bad64-317">设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。</span><span class="sxs-lookup"><span data-stu-id="bad64-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="bad64-318">`IFileInfo` 提供用于处理文件的方法和属性：</span><span class="sxs-lookup"><span data-stu-id="bad64-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="bad64-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）</span><span class="sxs-lookup"><span data-stu-id="bad64-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="bad64-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期</span><span class="sxs-lookup"><span data-stu-id="bad64-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="bad64-321">可以使用 [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 方法从文件读取内容。</span><span class="sxs-lookup"><span data-stu-id="bad64-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="bad64-322">示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖关系注入](xref:fundamentals/dependency-injection)在应用中使用。</span><span class="sxs-lookup"><span data-stu-id="bad64-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="bad64-323">文件提供程序实现</span><span class="sxs-lookup"><span data-stu-id="bad64-323">File Provider implementations</span></span>

<span data-ttu-id="bad64-324">`IFileProvider` 有三种实现。</span><span class="sxs-lookup"><span data-stu-id="bad64-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="bad64-325">实现</span><span class="sxs-lookup"><span data-stu-id="bad64-325">Implementation</span></span> | <span data-ttu-id="bad64-326">描述</span><span class="sxs-lookup"><span data-stu-id="bad64-326">Description</span></span> |
| ---
<span data-ttu-id="bad64-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-328">'Blazor'</span></span>
- <span data-ttu-id="bad64-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-329">'Identity'</span></span>
- <span data-ttu-id="bad64-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-331">'Razor'</span></span>
- <span data-ttu-id="bad64-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-334">'Blazor'</span></span>
- <span data-ttu-id="bad64-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-335">'Identity'</span></span>
- <span data-ttu-id="bad64-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-337">'Razor'</span></span>
- <span data-ttu-id="bad64-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-340">'Blazor'</span></span>
- <span data-ttu-id="bad64-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-341">'Identity'</span></span>
- <span data-ttu-id="bad64-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-343">'Razor'</span></span>
- <span data-ttu-id="bad64-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-346">'Blazor'</span></span>
- <span data-ttu-id="bad64-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-347">'Identity'</span></span>
- <span data-ttu-id="bad64-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-349">'Razor'</span></span>
- <span data-ttu-id="bad64-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-352">'Blazor'</span></span>
- <span data-ttu-id="bad64-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-353">'Identity'</span></span>
- <span data-ttu-id="bad64-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-355">'Razor'</span></span>
- <span data-ttu-id="bad64-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-356">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-358">'Blazor'</span></span>
- <span data-ttu-id="bad64-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-359">'Identity'</span></span>
- <span data-ttu-id="bad64-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-361">'Razor'</span></span>
- <span data-ttu-id="bad64-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-364">'Blazor'</span></span>
- <span data-ttu-id="bad64-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-365">'Identity'</span></span>
- <span data-ttu-id="bad64-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-367">'Razor'</span></span>
- <span data-ttu-id="bad64-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-370">'Blazor'</span></span>
- <span data-ttu-id="bad64-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-371">'Identity'</span></span>
- <span data-ttu-id="bad64-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-373">'Razor'</span></span>
- <span data-ttu-id="bad64-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-374">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | 用于访问系统的物理文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="bad64-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | 清单嵌入式提供程序用于访问程序集中嵌入的文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="bad64-377">| | [CompositeFileProvider](#compositefileprovider) | 复合式提供程序用于提供对一个或多个其他提供程序中的文件和目录的合并访问。</span><span class="sxs-lookup"><span data-stu-id="bad64-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="bad64-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="bad64-378">PhysicalFileProvider</span></span>

<span data-ttu-id="bad64-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。</span><span class="sxs-lookup"><span data-stu-id="bad64-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="bad64-380">`PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。</span><span class="sxs-lookup"><span data-stu-id="bad64-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="bad64-381">此范围界定可防止访问指定目录和子目录之外的文件系统。</span><span class="sxs-lookup"><span data-stu-id="bad64-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="bad64-382">创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bad64-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="bad64-383">直接实例化此提供程序时，必须提供目录路径并将其作为使用提供程序发出的所有请求的基路径。</span><span class="sxs-lookup"><span data-stu-id="bad64-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="bad64-384">下面的代码演示如何创建 `PhysicalFileProvider` 并用它来获取目录内容和文件信息：</span><span class="sxs-lookup"><span data-stu-id="bad64-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="bad64-385">前面的示例中的类型：</span><span class="sxs-lookup"><span data-stu-id="bad64-385">Types in the preceding example:</span></span>

* <span data-ttu-id="bad64-386">`provider` 是 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="bad64-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="bad64-387">`contents` 是 `IDirectoryContents`。</span><span class="sxs-lookup"><span data-stu-id="bad64-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="bad64-388">`fileInfo` 是 `IFileInfo`。</span><span class="sxs-lookup"><span data-stu-id="bad64-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="bad64-389">文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。</span><span class="sxs-lookup"><span data-stu-id="bad64-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="bad64-390">该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bad64-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="bad64-391">示例应用使用 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) 在应用的 `Startup.ConfigureServices` 类中创建提供程序：</span><span class="sxs-lookup"><span data-stu-id="bad64-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="bad64-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="bad64-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="bad64-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="bad64-394">`ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。</span><span class="sxs-lookup"><span data-stu-id="bad64-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="bad64-395">若要生成嵌入的文件清单，请将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="bad64-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="bad64-396">指定要使用 [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：</span><span class="sxs-lookup"><span data-stu-id="bad64-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="bad64-397">使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="bad64-398">示例应用创建 `ManifestEmbeddedFileProvider` 并将当前正在执行的程序集传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bad64-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="bad64-399">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="bad64-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="bad64-400">其他重载使你能够：</span><span class="sxs-lookup"><span data-stu-id="bad64-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="bad64-401">指定相对文件路径。</span><span class="sxs-lookup"><span data-stu-id="bad64-401">Specify a relative file path.</span></span>
* <span data-ttu-id="bad64-402">将文件范围限制到上次修改日期。</span><span class="sxs-lookup"><span data-stu-id="bad64-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="bad64-403">为包含嵌入文件清单的嵌入资源命名。</span><span class="sxs-lookup"><span data-stu-id="bad64-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="bad64-404">重载</span><span class="sxs-lookup"><span data-stu-id="bad64-404">Overload</span></span> | <span data-ttu-id="bad64-405">描述</span><span class="sxs-lookup"><span data-stu-id="bad64-405">Description</span></span> |
| ---
<span data-ttu-id="bad64-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-407">'Blazor'</span></span>
- <span data-ttu-id="bad64-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-408">'Identity'</span></span>
- <span data-ttu-id="bad64-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-410">'Razor'</span></span>
- <span data-ttu-id="bad64-411">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-413">'Blazor'</span></span>
- <span data-ttu-id="bad64-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-414">'Identity'</span></span>
- <span data-ttu-id="bad64-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-416">'Razor'</span></span>
- <span data-ttu-id="bad64-417">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-417">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-419">'Blazor'</span></span>
- <span data-ttu-id="bad64-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-420">'Identity'</span></span>
- <span data-ttu-id="bad64-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-422">'Razor'</span></span>
- <span data-ttu-id="bad64-423">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-425">'Blazor'</span></span>
- <span data-ttu-id="bad64-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-426">'Identity'</span></span>
- <span data-ttu-id="bad64-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-428">'Razor'</span></span>
- <span data-ttu-id="bad64-429">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bad64-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="bad64-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bad64-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bad64-431">'Blazor'</span></span>
- <span data-ttu-id="bad64-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bad64-432">'Identity'</span></span>
- <span data-ttu-id="bad64-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bad64-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="bad64-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bad64-434">'Razor'</span></span>
- <span data-ttu-id="bad64-435">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bad64-435">'SignalR' uid:</span></span> 

<span data-ttu-id="bad64-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | 接受一个可选的 `root` 相对路径参数。</span><span class="sxs-lookup"><span data-stu-id="bad64-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="bad64-437">指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。</span><span class="sxs-lookup"><span data-stu-id="bad64-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="bad64-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。</span><span class="sxs-lookup"><span data-stu-id="bad64-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="bad64-439">`lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。</span><span class="sxs-lookup"><span data-stu-id="bad64-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="bad64-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。</span><span class="sxs-lookup"><span data-stu-id="bad64-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="bad64-441">`manifestName` 表示包含清单的嵌入资源的名称。</span><span class="sxs-lookup"><span data-stu-id="bad64-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="bad64-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="bad64-442">CompositeFileProvider</span></span>

<span data-ttu-id="bad64-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="bad64-444">创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="bad64-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="bad64-445">在示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件：</span><span class="sxs-lookup"><span data-stu-id="bad64-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="bad64-446">监视更改</span><span class="sxs-lookup"><span data-stu-id="bad64-446">Watch for changes</span></span>

<span data-ttu-id="bad64-447">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 方法提供了一个方案来监视一个或多个文件或目录是否发生更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="bad64-448">`Watch` 接受路径字符串，它可以使用 [glob 模式](#glob-patterns)指定多个文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="bad64-449">`Watch` 返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。</span><span class="sxs-lookup"><span data-stu-id="bad64-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="bad64-450">更改令牌会公开以下内容：</span><span class="sxs-lookup"><span data-stu-id="bad64-450">The change token exposes:</span></span>

* <span data-ttu-id="bad64-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>：可检查此属性以确定是否已发生更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="bad64-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>：检测到指定的路径字符串发生更改时调用此属性。</span><span class="sxs-lookup"><span data-stu-id="bad64-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="bad64-453">每个更改令牌仅调用其关联的回调来响应单个更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="bad64-454">若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。</span><span class="sxs-lookup"><span data-stu-id="bad64-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="bad64-455">在示例应用中，WatchConsole 控制台应用配置为每次修改了文本文件时显示一条消息：</span><span class="sxs-lookup"><span data-stu-id="bad64-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="bad64-456">某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。</span><span class="sxs-lookup"><span data-stu-id="bad64-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="bad64-457">将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。</span><span class="sxs-lookup"><span data-stu-id="bad64-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="bad64-458">glob 模式</span><span class="sxs-lookup"><span data-stu-id="bad64-458">Glob patterns</span></span>

<span data-ttu-id="bad64-459">文件系统路径使用名为 glob（或通配）模式的通配符模式。</span><span class="sxs-lookup"><span data-stu-id="bad64-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="bad64-460">使用这些模式指定文件的组。</span><span class="sxs-lookup"><span data-stu-id="bad64-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="bad64-461">两个通配符分别是 `*` 和 `**`：</span><span class="sxs-lookup"><span data-stu-id="bad64-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="bad64-462">匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="bad64-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="bad64-463">匹配由文件路径中的 `/` 和 `.` 字符终止。</span><span class="sxs-lookup"><span data-stu-id="bad64-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="bad64-464">匹配多个目录级别的任何内容。</span><span class="sxs-lookup"><span data-stu-id="bad64-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="bad64-465">可用于以递归方式匹配目录层次结构中的许多文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="bad64-466">**glob 模式示例**</span><span class="sxs-lookup"><span data-stu-id="bad64-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="bad64-467">匹配特定目录中的特定文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="bad64-468">匹配特定目录中带 .txt 扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="bad64-469">匹配正好位于“目录”文件夹中下一级目录中的所有 `appsettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="bad64-470">匹配在“目录”文件夹下任何位置找到的带 .txt 扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="bad64-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
