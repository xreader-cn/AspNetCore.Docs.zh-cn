---
title: ASP.NET Core 中的静态文件
author: rick-anderson
description: 了解如何在 ASP.NET Core Web 应用中提供和保护静态文件，以及如何配置静态文件托管中间件行为。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/static-files
ms.openlocfilehash: e6bda5dd60c62c7bdbfa81f34c14cfcd07e8d700
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744048"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="276dd-103">ASP.NET Core 中的静态文件</span><span class="sxs-lookup"><span data-stu-id="276dd-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="276dd-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="276dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="276dd-105">静态文件（如 HTML、CSS、图像和 JavaScript）是 ASP.NET Core 应用直接提供给客户端的资产。</span><span class="sxs-lookup"><span data-stu-id="276dd-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="276dd-106">需要进行一些配置才能提供这些文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="276dd-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="276dd-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="276dd-108">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="276dd-108">Serve static files</span></span>

<span data-ttu-id="276dd-109">静态文件存储在项目的 Web 根目录中。</span><span class="sxs-lookup"><span data-stu-id="276dd-109">Static files are stored within your project's web root directory.</span></span> <span data-ttu-id="276dd-110">默认目录是 \<content_root>/wwwroot，但可通过 [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) 方法更改目录。</span><span class="sxs-lookup"><span data-stu-id="276dd-110">The default directory is *\<content_root>/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="276dd-111">有关详细信息，请参阅[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="276dd-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="276dd-112">应用的 Web 主机必须识别内容根目录。</span><span class="sxs-lookup"><span data-stu-id="276dd-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="276dd-113">采用 `WebHost.CreateDefaultBuilder` 方法可将内容根目录设置为当前目录：</span><span class="sxs-lookup"><span data-stu-id="276dd-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="276dd-114">调用 `Program.Main` 中的 [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) 将内容根目录设为当前目录：</span><span class="sxs-lookup"><span data-stu-id="276dd-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="276dd-115">可通过 Web 根目录的相关路径访问静态文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-115">Static files are accessible via a path relative to the web root.</span></span> <span data-ttu-id="276dd-116">例如，Web 应用程序项目模板包含 wwwroot 文件夹中的多个文件夹：</span><span class="sxs-lookup"><span data-stu-id="276dd-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="276dd-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="276dd-117">**wwwroot**</span></span>
  * <span data-ttu-id="276dd-118">**css**</span><span class="sxs-lookup"><span data-stu-id="276dd-118">**css**</span></span>
  * <span data-ttu-id="276dd-119">**images**</span><span class="sxs-lookup"><span data-stu-id="276dd-119">**images**</span></span>
  * <span data-ttu-id="276dd-120">**js**</span><span class="sxs-lookup"><span data-stu-id="276dd-120">**js**</span></span>

<span data-ttu-id="276dd-121">用于访问 images 子文件夹中的文件的 URI 格式为 http://\<server_address>/images/\<image_file_name>。</span><span class="sxs-lookup"><span data-stu-id="276dd-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="276dd-122">例如，*http://localhost:9189/images/banner3.svg*。</span><span class="sxs-lookup"><span data-stu-id="276dd-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="276dd-123">如果以 .NET Framework 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="276dd-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="276dd-124">如果以 .NET Core 为目标，[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)将包括此包。</span><span class="sxs-lookup"><span data-stu-id="276dd-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="276dd-125">如果以 .NET Framework 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="276dd-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="276dd-126">如果以 .NET Core 为目标，请将 [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)加入此包。</span><span class="sxs-lookup"><span data-stu-id="276dd-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="276dd-127">将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="276dd-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span>

::: moniker-end

<span data-ttu-id="276dd-128">配置提供静态文件的[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="276dd-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="276dd-129">提供 Web 根目录内的文件</span><span class="sxs-lookup"><span data-stu-id="276dd-129">Serve files inside of web root</span></span>

<span data-ttu-id="276dd-130">调用 `Startup.Configure` 中的 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 方法：</span><span class="sxs-lookup"><span data-stu-id="276dd-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="276dd-131">无参数 `UseStaticFiles` 方法重载将 Web 根目录中的文件标记为可用。</span><span class="sxs-lookup"><span data-stu-id="276dd-131">The parameterless `UseStaticFiles` method overload marks the files in web root as servable.</span></span> <span data-ttu-id="276dd-132">以下标记引用 wwwroot/images/banner1.svg：</span><span class="sxs-lookup"><span data-stu-id="276dd-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="276dd-133">在上面的代码中，波形符 `~/` 指向 Web 根目录。</span><span class="sxs-lookup"><span data-stu-id="276dd-133">In the preceding code, the tilde character `~/` points to webroot.</span></span> <span data-ttu-id="276dd-134">有关详细信息，请参阅 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="276dd-134">For more information, see [Web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="276dd-135">提供 Web 根目录外的文件</span><span class="sxs-lookup"><span data-stu-id="276dd-135">Serve files outside of web root</span></span>

<span data-ttu-id="276dd-136">考虑一个目录层次结构，其中要提供的静态文件位于 Web 根目录之外：</span><span class="sxs-lookup"><span data-stu-id="276dd-136">Consider a directory hierarchy in which the static files to be served reside outside of the web root:</span></span>

* <span data-ttu-id="276dd-137">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="276dd-137">**wwwroot**</span></span>
  * <span data-ttu-id="276dd-138">**css**</span><span class="sxs-lookup"><span data-stu-id="276dd-138">**css**</span></span>
  * <span data-ttu-id="276dd-139">**images**</span><span class="sxs-lookup"><span data-stu-id="276dd-139">**images**</span></span>
  * <span data-ttu-id="276dd-140">**js**</span><span class="sxs-lookup"><span data-stu-id="276dd-140">**js**</span></span>
* <span data-ttu-id="276dd-141">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="276dd-141">**MyStaticFiles**</span></span>
  * <span data-ttu-id="276dd-142">**images**</span><span class="sxs-lookup"><span data-stu-id="276dd-142">**images**</span></span>
      * <span data-ttu-id="276dd-143">banner1.svg</span><span class="sxs-lookup"><span data-stu-id="276dd-143">*banner1.svg*</span></span>

<span data-ttu-id="276dd-144">按如下方式配置静态文件中间件后，请求可访问 banner1.svg 文件：</span><span class="sxs-lookup"><span data-stu-id="276dd-144">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="276dd-145">在前面的代码中，MyStaticFiles 目录层次结构通过 StaticFiles URI 段公开。</span><span class="sxs-lookup"><span data-stu-id="276dd-145">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="276dd-146">请求 http://\<server_address>/StaticFiles/images/banner1.svg 提供 banner1.svg 文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-146">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="276dd-147">以下标记引用 MyStaticFiles/images/banner1.svg：</span><span class="sxs-lookup"><span data-stu-id="276dd-147">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="276dd-148">设置 HTTP 响应标头</span><span class="sxs-lookup"><span data-stu-id="276dd-148">Set HTTP response headers</span></span>

<span data-ttu-id="276dd-149">[StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) 对象可用于设置 HTTP 响应标头。</span><span class="sxs-lookup"><span data-stu-id="276dd-149">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="276dd-150">除配置从 Web 根目录提供静态文件外，以下代码还设置 `Cache-Control` 标头：</span><span class="sxs-lookup"><span data-stu-id="276dd-150">In addition to configuring static file serving from the web root, the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="276dd-151">[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) 方法存在于 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 包中。</span><span class="sxs-lookup"><span data-stu-id="276dd-151">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="276dd-152">在开发环境中可公开缓存这些文件 10 分钟（600 秒）：</span><span class="sxs-lookup"><span data-stu-id="276dd-152">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![已添加显示 Cache-Control 标头的响应头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="276dd-154">静态文件授权</span><span class="sxs-lookup"><span data-stu-id="276dd-154">Static file authorization</span></span>

<span data-ttu-id="276dd-155">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="276dd-155">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="276dd-156">可公开访问由静态文件中间件提供的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-156">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="276dd-157">根据授权提供文件：</span><span class="sxs-lookup"><span data-stu-id="276dd-157">To serve files based on authorization:</span></span>

* <span data-ttu-id="276dd-158">将文件存储在 wwwroot 和静态文件中间件可访问的任何目录之外。</span><span class="sxs-lookup"><span data-stu-id="276dd-158">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="276dd-159">通过有授权的操作方法提供文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-159">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="276dd-160">返回 [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) 对象：</span><span class="sxs-lookup"><span data-stu-id="276dd-160">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="276dd-161">启用目录浏览</span><span class="sxs-lookup"><span data-stu-id="276dd-161">Enable directory browsing</span></span>

<span data-ttu-id="276dd-162">通过目录浏览，Web 应用的用户可查看目录列表和指定目录中的文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-162">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="276dd-163">出于安全考虑，目录浏览默认处于禁用状态（请参阅[注意事项](#considerations)）。</span><span class="sxs-lookup"><span data-stu-id="276dd-163">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="276dd-164">调用 `Startup.Configure` 中的 [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) 方法来启用目录浏览：</span><span class="sxs-lookup"><span data-stu-id="276dd-164">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="276dd-165">调用 `Startup.ConfigureServices` 中的[AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 方法来添加所需服务：</span><span class="sxs-lookup"><span data-stu-id="276dd-165">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="276dd-166">上述代码允许使用 URL http://\<server_address>/MyImages 浏览 wwwroot/images 文件夹的目录，并链接到每个文件和文件夹：</span><span class="sxs-lookup"><span data-stu-id="276dd-166">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![目录浏览](static-files/_static/dir-browse.png)

<span data-ttu-id="276dd-168">有关启用浏览时的安全风险，请参阅[注意事项](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="276dd-168">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="276dd-169">请注意以下示例中的两个 `UseStaticFiles` 调用。</span><span class="sxs-lookup"><span data-stu-id="276dd-169">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="276dd-170">第一个调用提供 wwwroot 文件夹中的静态文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-170">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="276dd-171">第二个调用使用 URL http://\<server_address>/MyImages 浏览 wwwroot/images 文件夹的目录：</span><span class="sxs-lookup"><span data-stu-id="276dd-171">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="276dd-172">提供默认文档</span><span class="sxs-lookup"><span data-stu-id="276dd-172">Serve a default document</span></span>

<span data-ttu-id="276dd-173">设置默认主页为访问者访问网站时提供了逻辑起点。</span><span class="sxs-lookup"><span data-stu-id="276dd-173">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="276dd-174">若要在用户不完全限定 URI 的情况下提供默认页面，请调用 `Startup.Configure` 中的 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 方法：</span><span class="sxs-lookup"><span data-stu-id="276dd-174">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="276dd-175">要提供默认文件，必须在 `UseStaticFiles` 前调用 `UseDefaultFiles`。</span><span class="sxs-lookup"><span data-stu-id="276dd-175">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="276dd-176">`UseDefaultFiles` 实际上用于重写 URL，不提供文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-176">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="276dd-177">通过 `UseStaticFiles` 启用静态文件中间件来提供文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-177">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="276dd-178">使用 `UseDefaultFiles` 请求文件夹搜索：</span><span class="sxs-lookup"><span data-stu-id="276dd-178">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="276dd-179">default.htm</span><span class="sxs-lookup"><span data-stu-id="276dd-179">*default.htm*</span></span>
* <span data-ttu-id="276dd-180">default.html</span><span class="sxs-lookup"><span data-stu-id="276dd-180">*default.html*</span></span>
* <span data-ttu-id="276dd-181">index.htm</span><span class="sxs-lookup"><span data-stu-id="276dd-181">*index.htm*</span></span>
* <span data-ttu-id="276dd-182">index.html</span><span class="sxs-lookup"><span data-stu-id="276dd-182">*index.html*</span></span>

<span data-ttu-id="276dd-183">将请求视为完全限定 URI，提供在列表中找到的第一个文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-183">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="276dd-184">浏览器 URL 继续反映请求的 URI。</span><span class="sxs-lookup"><span data-stu-id="276dd-184">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="276dd-185">以下代码将默认文件名更改为 mydefault.html：</span><span class="sxs-lookup"><span data-stu-id="276dd-185">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="276dd-186">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="276dd-186">UseFileServer</span></span>

<span data-ttu-id="276dd-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 结合了 `UseStaticFiles`、`UseDefaultFiles` 和 `UseDirectoryBrowser` 的功能。</span><span class="sxs-lookup"><span data-stu-id="276dd-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="276dd-188">以下代码提供静态文件和默认文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-188">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="276dd-189">未启用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="276dd-189">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="276dd-190">以下代码通过启用目录浏览基于无参数重载进行构建：</span><span class="sxs-lookup"><span data-stu-id="276dd-190">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="276dd-191">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="276dd-191">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="276dd-192">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="276dd-192">**wwwroot**</span></span>
  * <span data-ttu-id="276dd-193">**css**</span><span class="sxs-lookup"><span data-stu-id="276dd-193">**css**</span></span>
  * <span data-ttu-id="276dd-194">**images**</span><span class="sxs-lookup"><span data-stu-id="276dd-194">**images**</span></span>
  * <span data-ttu-id="276dd-195">**js**</span><span class="sxs-lookup"><span data-stu-id="276dd-195">**js**</span></span>
* <span data-ttu-id="276dd-196">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="276dd-196">**MyStaticFiles**</span></span>
  * <span data-ttu-id="276dd-197">**images**</span><span class="sxs-lookup"><span data-stu-id="276dd-197">**images**</span></span>
      * <span data-ttu-id="276dd-198">banner1.svg</span><span class="sxs-lookup"><span data-stu-id="276dd-198">*banner1.svg*</span></span>
  * <span data-ttu-id="276dd-199">default.html</span><span class="sxs-lookup"><span data-stu-id="276dd-199">*default.html*</span></span>

<span data-ttu-id="276dd-200">以下代码启用静态文件、默认文件和及 `MyStaticFiles` 的目录浏览：</span><span class="sxs-lookup"><span data-stu-id="276dd-200">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="276dd-201">`EnableDirectoryBrowsing` 属性值为 `true` 时必须调用 `AddDirectoryBrowser`：</span><span class="sxs-lookup"><span data-stu-id="276dd-201">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="276dd-202">使用文件层次结构和前面的代码，URL 解析如下：</span><span class="sxs-lookup"><span data-stu-id="276dd-202">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="276dd-203">URI</span><span class="sxs-lookup"><span data-stu-id="276dd-203">URI</span></span>            |                             <span data-ttu-id="276dd-204">响应</span><span class="sxs-lookup"><span data-stu-id="276dd-204">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="276dd-205">http://\<server_address>/StaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="276dd-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="276dd-206">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="276dd-206">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="276dd-207">http://\<server_address>/StaticFiles</span><span class="sxs-lookup"><span data-stu-id="276dd-207">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="276dd-208">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="276dd-208">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="276dd-209">如果 MyStaticFiles 目录中不存在默认命名文件，则 http://\<server_address>/StaticFiles 返回包含可单击链接的目录列表：</span><span class="sxs-lookup"><span data-stu-id="276dd-209">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![静态文件列表](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="276dd-211">`UseDefaultFiles` 和 `UseDirectoryBrowser` 使用不带尾部反斜杠的 URL http://\<server_address>/StaticFiles 触发客户端并重定向到 http://\<server_address>/StaticFiles/。</span><span class="sxs-lookup"><span data-stu-id="276dd-211">`UseDefaultFiles` and `UseDirectoryBrowser` use the URL *http://\<server_address>/StaticFiles* without the trailing slash to trigger a client-side redirect to *http://\<server_address>/StaticFiles/*.</span></span> <span data-ttu-id="276dd-212">注意添加尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="276dd-212">Notice the addition of the trailing slash.</span></span> <span data-ttu-id="276dd-213">无尾部反斜杠，文档中的相对 URL 被视为无效。</span><span class="sxs-lookup"><span data-stu-id="276dd-213">Relative URLs within the documents are deemed invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="276dd-214">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="276dd-214">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="276dd-215">[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) 类包含 `Mappings` 属性，用作文件扩展名到 MIME 内容类型的映射。</span><span class="sxs-lookup"><span data-stu-id="276dd-215">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="276dd-216">在以下示例中，多个文件扩展名注册到了已知的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="276dd-216">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="276dd-217">替换了 .rtf 扩展名，删除了 .mp4。</span><span class="sxs-lookup"><span data-stu-id="276dd-217">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="276dd-218">请参阅 [MIME 内容类型](http://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="276dd-218">See [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="276dd-219">非标准内容类型</span><span class="sxs-lookup"><span data-stu-id="276dd-219">Non-standard content types</span></span>

<span data-ttu-id="276dd-220">静态文件中间件可理解近 400 种已知文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="276dd-220">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="276dd-221">如果用户请求文件类型未知的文件，则静态文件中间件将请求传递给管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="276dd-221">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="276dd-222">如果没有中间件处理请求，则返回“404 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="276dd-222">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="276dd-223">如果启用了目录浏览，则在目录列表中会显示该文件的链接。</span><span class="sxs-lookup"><span data-stu-id="276dd-223">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="276dd-224">以下代码提供未知类型，并以图像形式呈现未知文件：</span><span class="sxs-lookup"><span data-stu-id="276dd-224">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="276dd-225">使用前面的代码，请求的文件含未知内容类型时，以图像形式返回请求。</span><span class="sxs-lookup"><span data-stu-id="276dd-225">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="276dd-226">启用 [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) 存在安全风险。</span><span class="sxs-lookup"><span data-stu-id="276dd-226">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="276dd-227">它默认处于禁用状态，不建议使用。</span><span class="sxs-lookup"><span data-stu-id="276dd-227">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="276dd-228">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 提供了更安全的替代方法来提供含非标准扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-228">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="276dd-229">注意事项</span><span class="sxs-lookup"><span data-stu-id="276dd-229">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="276dd-230">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能会泄漏机密。</span><span class="sxs-lookup"><span data-stu-id="276dd-230">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="276dd-231">强烈建议在生产中禁用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="276dd-231">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="276dd-232">请仔细查看 `UseStaticFiles` 或 `UseDirectoryBrowser` 启用了哪些目录。</span><span class="sxs-lookup"><span data-stu-id="276dd-232">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="276dd-233">整个目录及其子目录均可公开访问。</span><span class="sxs-lookup"><span data-stu-id="276dd-233">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="276dd-234">将适合公开的文件存储在专用目录中，如 \<content_root>/wwwroot。</span><span class="sxs-lookup"><span data-stu-id="276dd-234">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="276dd-235">将这些文件与 MVC 视图、Razor 页面（仅限 2.x）和配置文件等分开</span><span class="sxs-lookup"><span data-stu-id="276dd-235">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="276dd-236">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公开的内容的 URL 受大小写和基础文件系统字符限制的影响。</span><span class="sxs-lookup"><span data-stu-id="276dd-236">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="276dd-237">例如，Windows 不区分大小写 &mdash; macOS 和 Linux 却要区分。</span><span class="sxs-lookup"><span data-stu-id="276dd-237">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="276dd-238">托管于 IIS 中的 ASP.NET Core 应用使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将所有请求转发到应用，包括静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="276dd-238">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="276dd-239">未使用 IIS 静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="276dd-239">The IIS static file handler isn't used.</span></span> <span data-ttu-id="276dd-240">在模块处理请求前，处理程序没有机会处理请求。</span><span class="sxs-lookup"><span data-stu-id="276dd-240">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="276dd-241">在 IIS Manager 中完成以下步骤，删除服务器或网站级别的 IIS 静态文件处理程序：</span><span class="sxs-lookup"><span data-stu-id="276dd-241">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="276dd-242">转到“模块”功能。</span><span class="sxs-lookup"><span data-stu-id="276dd-242">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="276dd-243">在列表中选择 StaticFileModule。</span><span class="sxs-lookup"><span data-stu-id="276dd-243">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="276dd-244">单击“操作”侧栏中的“删除”。</span><span class="sxs-lookup"><span data-stu-id="276dd-244">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="276dd-245">如果启用了 IIS 静态文件处理程序且 ASP.NET Core 模块配置不正确，则会提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="276dd-245">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="276dd-246">例如，如果未部署 web.config 文件，则会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="276dd-246">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="276dd-247">将代码文件（包括 .cs 和 .cshtml ）放在应用项目的 Web 根目录之外。</span><span class="sxs-lookup"><span data-stu-id="276dd-247">Place code files (including *.cs* and *.cshtml*) outside of the app project's web root.</span></span> <span data-ttu-id="276dd-248">这样就在应用的客户端内容和基于服务器的代码间创建了逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="276dd-248">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="276dd-249">可以防止服务器端代码泄漏。</span><span class="sxs-lookup"><span data-stu-id="276dd-249">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="276dd-250">其他资源</span><span class="sxs-lookup"><span data-stu-id="276dd-250">Additional resources</span></span>

* [<span data-ttu-id="276dd-251">中间件</span><span class="sxs-lookup"><span data-stu-id="276dd-251">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="276dd-252">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="276dd-252">Introduction to ASP.NET Core</span></span>](xref:index)
