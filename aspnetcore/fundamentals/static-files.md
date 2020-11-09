---
title: ASP.NET Core 中的静态文件
author: rick-anderson
description: 了解如何在 ASP.NET Core Web 应用中提供和保护静态文件，以及如何配置静态文件托管中间件行为。
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057851"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="eef22-103">ASP.NET Core 中的静态文件</span><span class="sxs-lookup"><span data-stu-id="eef22-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eef22-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="eef22-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="eef22-105">默认情况下，静态文件（如 HTML、CSS、图像和 JavaScript）是 ASP.NET Core 应用直接提供给客户端的资产。</span><span class="sxs-lookup"><span data-stu-id="eef22-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="eef22-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eef22-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="eef22-107">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="eef22-107">Serve static files</span></span>

<span data-ttu-id="eef22-108">静态文件存储在项目的 [Web 根](xref:fundamentals/index#web-root)目录中。</span><span class="sxs-lookup"><span data-stu-id="eef22-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="eef22-109">默认目录为 `{content root}/wwwroot`，但可通过 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 方法更改目录。</span><span class="sxs-lookup"><span data-stu-id="eef22-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="eef22-110">有关详细信息，请参阅[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="eef22-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="eef22-111">采用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 方法可将内容根目录设置为当前目录：</span><span class="sxs-lookup"><span data-stu-id="eef22-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="eef22-112">上述代码是使用 Web 应用程序模板创建的。</span><span class="sxs-lookup"><span data-stu-id="eef22-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="eef22-113">可通过 [Web 根目录](xref:fundamentals/index#web-root)的相关路径访问静态文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="eef22-114">例如，Web 应用程序项目模板包含 `wwwroot` 文件夹中的多个文件夹：</span><span class="sxs-lookup"><span data-stu-id="eef22-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="eef22-115">请考虑创建 wwwroot/images 文件夹，并添加 wwwroot/images/MyImage 文件。 </span><span class="sxs-lookup"><span data-stu-id="eef22-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="eef22-116">用于访问 `images` 文件夹中的文件的 URI 格式为 `https://<hostname>/images/<image_file_name>`。</span><span class="sxs-lookup"><span data-stu-id="eef22-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="eef22-117">例如，`https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="eef22-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="eef22-118">在 Web 根目录中提供文件</span><span class="sxs-lookup"><span data-stu-id="eef22-118">Serve files in web root</span></span>

<span data-ttu-id="eef22-119">默认 Web 应用模板在 `Startup.Configure` 中调用 <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> 方法，这将允许提供静态文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="eef22-120">无参数 `UseStaticFiles` 方法重载将 [Web 根目录](xref:fundamentals/index#web-root)中的文件标记为可用。</span><span class="sxs-lookup"><span data-stu-id="eef22-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="eef22-121">以下标记引用 wwwroot/images/MyImage.jpg：</span><span class="sxs-lookup"><span data-stu-id="eef22-121">The following markup references *wwwroot/images/MyImage.jpg* :</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="eef22-122">在上面的代码中，波形符 `~/` 指向 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="eef22-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="eef22-123">提供 Web 根目录外的文件</span><span class="sxs-lookup"><span data-stu-id="eef22-123">Serve files outside of web root</span></span>

<span data-ttu-id="eef22-124">考虑一个目录层次结构，其中要提供的静态文件位于 [Web 根目录](xref:fundamentals/index#web-root)之外：</span><span class="sxs-lookup"><span data-stu-id="eef22-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="eef22-125">按如下方式配置静态文件中间件后，请求可访问 `red-rose.jpg` 文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="eef22-126">在前面的代码中，MyStaticFiles 目录层次结构通过 StaticFiles URI 段公开 。</span><span class="sxs-lookup"><span data-stu-id="eef22-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="eef22-127">对 `https://<hostname>/StaticFiles/images/red-rose.jpg` 的请求将提供 red-rose.jpg 文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="eef22-128">以下标记引用 MyStaticFiles/images/red-rose.jpg：</span><span class="sxs-lookup"><span data-stu-id="eef22-128">The following markup references *MyStaticFiles/images/red-rose.jpg* :</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="eef22-129">设置 HTTP 响应标头</span><span class="sxs-lookup"><span data-stu-id="eef22-129">Set HTTP response headers</span></span>

<span data-ttu-id="eef22-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 对象可用于设置 HTTP 响应标头。</span><span class="sxs-lookup"><span data-stu-id="eef22-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="eef22-131">除配置从 [Web 根目录](xref:fundamentals/index#web-root)提供静态文件外，以下代码还设置 `Cache-Control` 标头：</span><span class="sxs-lookup"><span data-stu-id="eef22-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="eef22-132">静态文件可在 600 秒内公开缓存：</span><span class="sxs-lookup"><span data-stu-id="eef22-132">Static files are publicly cacheable for 600 seconds:</span></span>

![已添加显示 Cache-Control 标头的响应头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="eef22-134">静态文件授权</span><span class="sxs-lookup"><span data-stu-id="eef22-134">Static file authorization</span></span>

<span data-ttu-id="eef22-135">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="eef22-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="eef22-136">可公开访问由静态文件中间件提供的任何文件，包括 `wwwroot` 下的文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="eef22-137">根据授权提供文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="eef22-138">将文件存储在 `wwwroot` 和默认静态文件中间件可访问的任何目录之外。</span><span class="sxs-lookup"><span data-stu-id="eef22-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="eef22-139">在 `UseAuthorization` 后面调用 `UseStaticFiles` 并指定路径：</span><span class="sxs-lookup"><span data-stu-id="eef22-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="eef22-140">上述方法要求用户已进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="eef22-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="eef22-141">还有一种根据授权提供文件的方法是：</span><span class="sxs-lookup"><span data-stu-id="eef22-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="eef22-142">将文件存储在 `wwwroot` 和静态文件中间件可访问的任何目录之外。</span><span class="sxs-lookup"><span data-stu-id="eef22-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="eef22-143">通过应用授权的操作方法为其提供服务，并返回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 对象：</span><span class="sxs-lookup"><span data-stu-id="eef22-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="eef22-144">目录浏览</span><span class="sxs-lookup"><span data-stu-id="eef22-144">Directory browsing</span></span>

<span data-ttu-id="eef22-145">目录浏览允许在指定目录中列出目录。</span><span class="sxs-lookup"><span data-stu-id="eef22-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="eef22-146">出于安全考虑，目录浏览默认处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="eef22-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="eef22-147">有关详细信息，请参阅[注意事项](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="eef22-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="eef22-148">通过以下方式启用目录浏览：</span><span class="sxs-lookup"><span data-stu-id="eef22-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="eef22-149">`Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>。</span><span class="sxs-lookup"><span data-stu-id="eef22-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="eef22-150">`Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A>。</span><span class="sxs-lookup"><span data-stu-id="eef22-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="eef22-151">上述代码允许使用 URL `https://<hostname>/MyImages` 浏览 wwwroot/images 文件夹的目录，并链接到每个文件和文件夹：</span><span class="sxs-lookup"><span data-stu-id="eef22-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![目录浏览](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="eef22-153">提供默认文档</span><span class="sxs-lookup"><span data-stu-id="eef22-153">Serve default documents</span></span>

<span data-ttu-id="eef22-154">设置默认页面为访问者提供网站的起点。</span><span class="sxs-lookup"><span data-stu-id="eef22-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="eef22-155">若要在没有完全限定 URI 的情况下提供 `wwwroot` 的默认页面，请调用 <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="eef22-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="eef22-156">要提供默认文件，必须在 `UseStaticFiles` 前调用 `UseDefaultFiles`。</span><span class="sxs-lookup"><span data-stu-id="eef22-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="eef22-157">`UseDefaultFiles` 是不提供文件的 URL 重写工具。</span><span class="sxs-lookup"><span data-stu-id="eef22-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="eef22-158">使用 `UseDefaultFiles` 请求对 `wwwroot` 中的文件夹搜索：</span><span class="sxs-lookup"><span data-stu-id="eef22-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="eef22-159">default.htm</span><span class="sxs-lookup"><span data-stu-id="eef22-159">*default.htm*</span></span>
* <span data-ttu-id="eef22-160">default.html</span><span class="sxs-lookup"><span data-stu-id="eef22-160">*default.html*</span></span>
* <span data-ttu-id="eef22-161">index.htm</span><span class="sxs-lookup"><span data-stu-id="eef22-161">*index.htm*</span></span>
* <span data-ttu-id="eef22-162">index.html</span><span class="sxs-lookup"><span data-stu-id="eef22-162">*index.html*</span></span>

<span data-ttu-id="eef22-163">将请求视为完全限定 URI，提供在列表中找到的第一个文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="eef22-164">浏览器 URL 继续反映请求的 URI。</span><span class="sxs-lookup"><span data-stu-id="eef22-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="eef22-165">以下代码将默认文件名更改为 mydefault.html：</span><span class="sxs-lookup"><span data-stu-id="eef22-165">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="eef22-166">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="eef22-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="eef22-167">默认文档的 UseFileServer</span><span class="sxs-lookup"><span data-stu-id="eef22-167">UseFileServer for default documents</span></span>

<span data-ttu-id="eef22-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 结合了 `UseStaticFiles`、`UseDefaultFiles` 和 `UseDirectoryBrowser`（可选）的功能。</span><span class="sxs-lookup"><span data-stu-id="eef22-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="eef22-169">调用 `app.UseFileServer` 以提供静态文件和默认文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="eef22-170">未启用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="eef22-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="eef22-171">以下代码通过 `UseFileServer` 演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="eef22-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="eef22-172">以下代码提供静态文件、默认文件和目录浏览：</span><span class="sxs-lookup"><span data-stu-id="eef22-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="eef22-173">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="eef22-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="eef22-174">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="eef22-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="eef22-175">以下代码提供静态文件、默认文件和 `MyStaticFiles` 的目录浏览：</span><span class="sxs-lookup"><span data-stu-id="eef22-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="eef22-176">必须在 `EnableDirectoryBrowsing` 属性值为 `true` 时调用 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>。</span><span class="sxs-lookup"><span data-stu-id="eef22-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="eef22-177">使用文件层次结构和前面的代码，URL 解析如下：</span><span class="sxs-lookup"><span data-stu-id="eef22-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="eef22-178">URI</span><span class="sxs-lookup"><span data-stu-id="eef22-178">URI</span></span>            |      <span data-ttu-id="eef22-179">响应</span><span class="sxs-lookup"><span data-stu-id="eef22-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="eef22-180">MyStaticFiles/images/MyImage.jpg</span><span class="sxs-lookup"><span data-stu-id="eef22-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="eef22-181">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="eef22-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="eef22-182">如果 MyStaticFiles 目录中不存在默认命名文件，则 `https://<hostname>/StaticFiles` 返回包含可单击链接的目录列表：</span><span class="sxs-lookup"><span data-stu-id="eef22-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![静态文件列表](static-files/_static/db2.png)

<span data-ttu-id="eef22-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 执行从不带尾部 `/` 的目标 URI 到带尾部 `/` 的目标 URI 的客户端重定向。</span><span class="sxs-lookup"><span data-stu-id="eef22-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="eef22-185">例如，从 `https://<hostname>/StaticFiles` 到 `https://<hostname>/StaticFiles/`。</span><span class="sxs-lookup"><span data-stu-id="eef22-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="eef22-186">如果没有尾部斜杠 (`/`)，StaticFiles 目录中的相对 URL 无效。</span><span class="sxs-lookup"><span data-stu-id="eef22-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="eef22-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="eef22-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="eef22-188"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 类包含 `Mappings` 属性，用作文件扩展名到 MIME 内容类型的映射。</span><span class="sxs-lookup"><span data-stu-id="eef22-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="eef22-189">在以下示例中，多个文件扩展名映射到了已知的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="eef22-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="eef22-190">替换了 .rtf 扩展名，删除了 .mp4 ：</span><span class="sxs-lookup"><span data-stu-id="eef22-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="eef22-191">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="eef22-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="eef22-192">请参阅 [MIME 内容类型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="eef22-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="eef22-193">非标准内容类型</span><span class="sxs-lookup"><span data-stu-id="eef22-193">Non-standard content types</span></span>

<span data-ttu-id="eef22-194">静态文件中间件可理解近 400 种已知文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="eef22-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="eef22-195">如果用户请求文件类型未知的文件，则静态文件中间件将请求传递给管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="eef22-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="eef22-196">如果没有中间件处理请求，则返回“404 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="eef22-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="eef22-197">如果启用了目录浏览，则在目录列表中会显示该文件的链接。</span><span class="sxs-lookup"><span data-stu-id="eef22-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="eef22-198">以下代码提供未知类型，并以图像形式呈现未知文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="eef22-199">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="eef22-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="eef22-200">使用前面的代码，请求的文件含未知内容类型时，以图像形式返回请求。</span><span class="sxs-lookup"><span data-stu-id="eef22-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="eef22-201">启用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 会形成安全隐患。</span><span class="sxs-lookup"><span data-stu-id="eef22-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="eef22-202">它默认处于禁用状态，不建议使用。</span><span class="sxs-lookup"><span data-stu-id="eef22-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="eef22-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 提供了更安全的替代方法来提供含非标准扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="eef22-204">从多个位置提供文件</span><span class="sxs-lookup"><span data-stu-id="eef22-204">Serve files from multiple locations</span></span>

<span data-ttu-id="eef22-205">`UseStaticFiles` 和 `UseFileServer` 默认为指向 `wwwroot` 的文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="eef22-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="eef22-206">可使用其他文件提供程序提供 `UseStaticFiles` 和 `UseFileServer` 的其他实例，从多个位置提供文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="eef22-207">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="eef22-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="eef22-208">静态文件的安全注意事项</span><span class="sxs-lookup"><span data-stu-id="eef22-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="eef22-209">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能会泄漏机密。</span><span class="sxs-lookup"><span data-stu-id="eef22-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="eef22-210">强烈建议在生产中禁用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="eef22-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="eef22-211">请仔细查看 `UseStaticFiles` 或 `UseDirectoryBrowser` 启用了哪些目录。</span><span class="sxs-lookup"><span data-stu-id="eef22-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="eef22-212">整个目录及其子目录均可公开访问。</span><span class="sxs-lookup"><span data-stu-id="eef22-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="eef22-213">将适合公开的文件存储在专用目录中，如 `<content_root>/wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="eef22-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="eef22-214">将这些文件与 MVC 视图、Razor Pages 和配置文件等分开。</span><span class="sxs-lookup"><span data-stu-id="eef22-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="eef22-215">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公开的内容的 URL 受大小写和基础文件系统字符限制的影响。</span><span class="sxs-lookup"><span data-stu-id="eef22-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="eef22-216">例如，Windows 不区分大小写，但 macOS 和 Linux 区分大小写。</span><span class="sxs-lookup"><span data-stu-id="eef22-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="eef22-217">托管于 IIS 中的 ASP.NET Core 应用使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将所有请求转发到应用，包括静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="eef22-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="eef22-218">不使用 IIS 静态文件处理程序，并且没有机会处理请求。</span><span class="sxs-lookup"><span data-stu-id="eef22-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="eef22-219">在 IIS Manager 中完成以下步骤，删除服务器或网站级别的 IIS 静态文件处理程序：</span><span class="sxs-lookup"><span data-stu-id="eef22-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="eef22-220">转到“模块”功能。</span><span class="sxs-lookup"><span data-stu-id="eef22-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="eef22-221">在列表中选择 StaticFileModule。</span><span class="sxs-lookup"><span data-stu-id="eef22-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="eef22-222">单击“操作”侧栏中的“删除” 。</span><span class="sxs-lookup"><span data-stu-id="eef22-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="eef22-223">如果启用了 IIS 静态文件处理程序且 ASP.NET Core 模块配置不正确，则会提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="eef22-224">例如，如果未部署 web.config 文件，则会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="eef22-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="eef22-225">将代码文件（包括 .cs 和 .cshtml）放在应用项目的 [Web 根目录](xref:fundamentals/index#web-root)之外 。</span><span class="sxs-lookup"><span data-stu-id="eef22-225">Place code files, including *.cs* and *.cshtml* , outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="eef22-226">这样就在应用的客户端内容和基于服务器的代码间创建了逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="eef22-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="eef22-227">可以防止服务器端代码泄漏。</span><span class="sxs-lookup"><span data-stu-id="eef22-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eef22-228">其他资源</span><span class="sxs-lookup"><span data-stu-id="eef22-228">Additional resources</span></span>

* [<span data-ttu-id="eef22-229">中间件</span><span class="sxs-lookup"><span data-stu-id="eef22-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="eef22-230">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="eef22-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eef22-231">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="eef22-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="eef22-232">静态文件（如 HTML、CSS、图像和 JavaScript）是 ASP.NET Core 应用直接提供给客户端的资产。</span><span class="sxs-lookup"><span data-stu-id="eef22-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="eef22-233">需要进行一些配置才能提供这些文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="eef22-234">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eef22-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="eef22-235">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="eef22-235">Serve static files</span></span>

<span data-ttu-id="eef22-236">静态文件存储在项目的 [Web 根](xref:fundamentals/index#web-root)目录中。</span><span class="sxs-lookup"><span data-stu-id="eef22-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="eef22-237">默认目录是 {content root}/wwwroot，但可通过 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 方法更改目录。</span><span class="sxs-lookup"><span data-stu-id="eef22-237">The default directory is *{content root}/wwwroot* , but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="eef22-238">有关详细信息，请参阅[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="eef22-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="eef22-239">应用的 Web 主机必须识别内容根目录。</span><span class="sxs-lookup"><span data-stu-id="eef22-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="eef22-240">采用 `WebHost.CreateDefaultBuilder` 方法可将内容根目录设置为当前目录：</span><span class="sxs-lookup"><span data-stu-id="eef22-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="eef22-241">可通过 [Web 根目录](xref:fundamentals/index#web-root)的相关路径访问静态文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="eef22-242">例如，Web 应用程序项目模板包含 `wwwroot` 文件夹中的多个文件夹：</span><span class="sxs-lookup"><span data-stu-id="eef22-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="eef22-243">用于访问 images 子文件夹中的文件的 URI 格式为 http://\<server_address>/images/\<image_file_name> 。</span><span class="sxs-lookup"><span data-stu-id="eef22-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="eef22-244">例如， *http://localhost:9189/images/banner3.svg* 。</span><span class="sxs-lookup"><span data-stu-id="eef22-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="eef22-245">如果以 .NET Framework 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="eef22-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="eef22-246">如果以 .NET Core 为目标，[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)将包括此包。</span><span class="sxs-lookup"><span data-stu-id="eef22-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="eef22-247">配置提供静态文件的[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="eef22-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="eef22-248">提供 Web 根目录内的文件</span><span class="sxs-lookup"><span data-stu-id="eef22-248">Serve files inside of web root</span></span>

<span data-ttu-id="eef22-249">调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="eef22-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="eef22-250">无参数 `UseStaticFiles` 方法重载将 [Web 根目录](xref:fundamentals/index#web-root)中的文件标记为可用。</span><span class="sxs-lookup"><span data-stu-id="eef22-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="eef22-251">以下标记引用 wwwroot/images/banner1.svg：</span><span class="sxs-lookup"><span data-stu-id="eef22-251">The following markup references *wwwroot/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="eef22-252">在上面的代码中，波形符 `~/` 指向 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="eef22-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="eef22-253">提供 Web 根目录外的文件</span><span class="sxs-lookup"><span data-stu-id="eef22-253">Serve files outside of web root</span></span>

<span data-ttu-id="eef22-254">考虑一个目录层次结构，其中要提供的静态文件位于 [Web 根目录](xref:fundamentals/index#web-root)之外：</span><span class="sxs-lookup"><span data-stu-id="eef22-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="eef22-255">按如下方式配置静态文件中间件后，请求可访问 banner1.svg 文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="eef22-256">在前面的代码中，MyStaticFiles 目录层次结构通过 StaticFiles URI 段公开 。</span><span class="sxs-lookup"><span data-stu-id="eef22-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="eef22-257">请求 http://\<server_address>/StaticFiles/images/banner1.svg 提供 banner1.svg 文件 。</span><span class="sxs-lookup"><span data-stu-id="eef22-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="eef22-258">以下标记引用 MyStaticFiles/images/banner1.svg：</span><span class="sxs-lookup"><span data-stu-id="eef22-258">The following markup references *MyStaticFiles/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="eef22-259">设置 HTTP 响应标头</span><span class="sxs-lookup"><span data-stu-id="eef22-259">Set HTTP response headers</span></span>

<span data-ttu-id="eef22-260"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 对象可用于设置 HTTP 响应标头。</span><span class="sxs-lookup"><span data-stu-id="eef22-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="eef22-261">除配置从 [Web 根目录](xref:fundamentals/index#web-root)提供静态文件外，以下代码还设置 `Cache-Control` 标头：</span><span class="sxs-lookup"><span data-stu-id="eef22-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="eef22-262"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> 方法位于 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 包中。</span><span class="sxs-lookup"><span data-stu-id="eef22-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="eef22-263">在开发环境中可公开缓存这些文件 10 分钟（600 秒）：</span><span class="sxs-lookup"><span data-stu-id="eef22-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![已添加显示 Cache-Control 标头的响应头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="eef22-265">静态文件授权</span><span class="sxs-lookup"><span data-stu-id="eef22-265">Static file authorization</span></span>

<span data-ttu-id="eef22-266">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="eef22-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="eef22-267">可公开访问由静态文件中间件提供的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-267">Any files served by it, including those under *wwwroot* , are publicly accessible.</span></span> <span data-ttu-id="eef22-268">根据授权提供文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="eef22-269">将文件存储在 wwwroot 和静态文件中间件可访问的任何目录之外。</span><span class="sxs-lookup"><span data-stu-id="eef22-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="eef22-270">通过有授权的操作方法提供文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="eef22-271">返回一个 <xref:Microsoft.AspNetCore.Mvc.FileResult> 对象：</span><span class="sxs-lookup"><span data-stu-id="eef22-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="eef22-272">启用目录浏览</span><span class="sxs-lookup"><span data-stu-id="eef22-272">Enable directory browsing</span></span>

<span data-ttu-id="eef22-273">通过目录浏览，Web 应用的用户可查看目录列表和指定目录中的文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="eef22-274">出于安全考虑，目录浏览默认处于禁用状态（请参阅[注意事项](#considerations)）。</span><span class="sxs-lookup"><span data-stu-id="eef22-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="eef22-275">调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 方法来启用目录浏览：</span><span class="sxs-lookup"><span data-stu-id="eef22-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="eef22-276">通过从 `Startup.ConfigureServices` 调用 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 方法添加所需服务：</span><span class="sxs-lookup"><span data-stu-id="eef22-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="eef22-277">上述代码允许使用 URL http://\<server_address>/MyImages 浏览 wwwroot/images 文件夹的目录，并链接到每个文件和文件夹 ：</span><span class="sxs-lookup"><span data-stu-id="eef22-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* , with links to each file and folder:</span></span>

![目录浏览](static-files/_static/dir-browse.png)

<span data-ttu-id="eef22-279">有关启用浏览时的安全风险，请参阅[注意事项](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="eef22-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="eef22-280">请注意以下示例中的两个 `UseStaticFiles` 调用。</span><span class="sxs-lookup"><span data-stu-id="eef22-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="eef22-281">第一个调用提供 wwwroot 文件夹中的静态文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="eef22-282">第二个调用使用 URL http://\<server_address>/MyImages 浏览 wwwroot/images 文件夹的目录 ：</span><span class="sxs-lookup"><span data-stu-id="eef22-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="eef22-283">提供默认文档</span><span class="sxs-lookup"><span data-stu-id="eef22-283">Serve a default document</span></span>

<span data-ttu-id="eef22-284">设置默认主页为访问者访问网站时提供了逻辑起点。</span><span class="sxs-lookup"><span data-stu-id="eef22-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="eef22-285">若要在用户不完全限定 URI 的情况下提供默认页面，请调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="eef22-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="eef22-286">要提供默认文件，必须在 `UseStaticFiles` 前调用 `UseDefaultFiles`。</span><span class="sxs-lookup"><span data-stu-id="eef22-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="eef22-287">`UseDefaultFiles` 实际上用于重写 URL，不提供文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="eef22-288">通过 `UseStaticFiles` 启用静态文件中间件来提供文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="eef22-289">使用 `UseDefaultFiles` 请求文件夹搜索：</span><span class="sxs-lookup"><span data-stu-id="eef22-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="eef22-290">default.htm</span><span class="sxs-lookup"><span data-stu-id="eef22-290">*default.htm*</span></span>
* <span data-ttu-id="eef22-291">default.html</span><span class="sxs-lookup"><span data-stu-id="eef22-291">*default.html*</span></span>
* <span data-ttu-id="eef22-292">index.htm</span><span class="sxs-lookup"><span data-stu-id="eef22-292">*index.htm*</span></span>
* <span data-ttu-id="eef22-293">index.html</span><span class="sxs-lookup"><span data-stu-id="eef22-293">*index.html*</span></span>

<span data-ttu-id="eef22-294">将请求视为完全限定 URI，提供在列表中找到的第一个文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="eef22-295">浏览器 URL 继续反映请求的 URI。</span><span class="sxs-lookup"><span data-stu-id="eef22-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="eef22-296">以下代码将默认文件名更改为 mydefault.html：</span><span class="sxs-lookup"><span data-stu-id="eef22-296">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="eef22-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="eef22-297">UseFileServer</span></span>

<span data-ttu-id="eef22-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 结合了 `UseStaticFiles`、`UseDefaultFiles` 和 `UseDirectoryBrowser`（可选）的功能。</span><span class="sxs-lookup"><span data-stu-id="eef22-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="eef22-299">以下代码提供静态文件和默认文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="eef22-300">未启用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="eef22-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="eef22-301">以下代码通过启用目录浏览基于无参数重载进行构建：</span><span class="sxs-lookup"><span data-stu-id="eef22-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="eef22-302">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="eef22-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="eef22-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="eef22-303">**wwwroot**</span></span>
  * <span data-ttu-id="eef22-304">**css**</span><span class="sxs-lookup"><span data-stu-id="eef22-304">**css**</span></span>
  * <span data-ttu-id="eef22-305">**images**</span><span class="sxs-lookup"><span data-stu-id="eef22-305">**images**</span></span>
  * <span data-ttu-id="eef22-306">**js**</span><span class="sxs-lookup"><span data-stu-id="eef22-306">**js**</span></span>
* <span data-ttu-id="eef22-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="eef22-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="eef22-308">**images**</span><span class="sxs-lookup"><span data-stu-id="eef22-308">**images**</span></span>
    * <span data-ttu-id="eef22-309">banner1.svg</span><span class="sxs-lookup"><span data-stu-id="eef22-309">*banner1.svg*</span></span>
  * <span data-ttu-id="eef22-310">default.html</span><span class="sxs-lookup"><span data-stu-id="eef22-310">*default.html*</span></span>

<span data-ttu-id="eef22-311">以下代码启用静态文件、默认文件和及 `MyStaticFiles` 的目录浏览：</span><span class="sxs-lookup"><span data-stu-id="eef22-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="eef22-312">`EnableDirectoryBrowsing` 属性值为 `true` 时必须调用 `AddDirectoryBrowser`：</span><span class="sxs-lookup"><span data-stu-id="eef22-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="eef22-313">使用文件层次结构和前面的代码，URL 解析如下：</span><span class="sxs-lookup"><span data-stu-id="eef22-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="eef22-314">URI</span><span class="sxs-lookup"><span data-stu-id="eef22-314">URI</span></span>            |                             <span data-ttu-id="eef22-315">响应</span><span class="sxs-lookup"><span data-stu-id="eef22-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="eef22-316"> http://\<server_address>/StaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="eef22-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="eef22-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="eef22-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="eef22-318"> http://\<server_address>/StaticFiles</span><span class="sxs-lookup"><span data-stu-id="eef22-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="eef22-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="eef22-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="eef22-320">如果 MyStaticFiles 目录中不存在默认命名文件，则 http://\<server_address>/StaticFiles 返回包含可单击链接的目录列表 ：</span><span class="sxs-lookup"><span data-stu-id="eef22-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![静态文件列表](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="eef22-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 执行从 `http://{SERVER ADDRESS}/StaticFiles`（不带尾部斜杠）到 `http://{SERVER ADDRESS}/StaticFiles/`（带尾部斜杠）的客户端重定向。</span><span class="sxs-lookup"><span data-stu-id="eef22-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="eef22-323">如果没有尾部斜杠，StaticFiles 目录中的相对 URL 无效。</span><span class="sxs-lookup"><span data-stu-id="eef22-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="eef22-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="eef22-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="eef22-325"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 类包含 `Mappings` 属性，用作文件扩展名到 MIME 内容类型的映射。</span><span class="sxs-lookup"><span data-stu-id="eef22-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="eef22-326">在以下示例中，多个文件扩展名注册到了已知的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="eef22-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="eef22-327">替换了 .rtf 扩展名，删除了 .mp4 。</span><span class="sxs-lookup"><span data-stu-id="eef22-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="eef22-328">请参阅 [MIME 内容类型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="eef22-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="eef22-329">若要了解如何使用自定义 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>，或者要配置 Blazor Server 应用中的其他 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>，请参阅 <xref:blazor/fundamentals/additional-scenarios#static-files>。</span><span class="sxs-lookup"><span data-stu-id="eef22-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="eef22-330">非标准内容类型</span><span class="sxs-lookup"><span data-stu-id="eef22-330">Non-standard content types</span></span>

<span data-ttu-id="eef22-331">静态文件中间件可理解近 400 种已知文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="eef22-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="eef22-332">如果用户请求文件类型未知的文件，则静态文件中间件将请求传递给管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="eef22-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="eef22-333">如果没有中间件处理请求，则返回“404 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="eef22-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="eef22-334">如果启用了目录浏览，则在目录列表中会显示该文件的链接。</span><span class="sxs-lookup"><span data-stu-id="eef22-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="eef22-335">以下代码提供未知类型，并以图像形式呈现未知文件：</span><span class="sxs-lookup"><span data-stu-id="eef22-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="eef22-336">使用前面的代码，请求的文件含未知内容类型时，以图像形式返回请求。</span><span class="sxs-lookup"><span data-stu-id="eef22-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="eef22-337">启用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 会形成安全隐患。</span><span class="sxs-lookup"><span data-stu-id="eef22-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="eef22-338">它默认处于禁用状态，不建议使用。</span><span class="sxs-lookup"><span data-stu-id="eef22-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="eef22-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 提供了更安全的替代方法来提供含非标准扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="eef22-340">从多个位置提供文件</span><span class="sxs-lookup"><span data-stu-id="eef22-340">Serve files from multiple locations</span></span>

<span data-ttu-id="eef22-341">`UseStaticFiles` 和 `UseFileServer` 默认为指向 wwwroot 的文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="eef22-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="eef22-342">可使用其他文件提供程序提供 `UseStaticFiles` 和 `UseFileServer` 的其他实例，从多个位置提供文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="eef22-343">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="eef22-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="eef22-344">注意事项</span><span class="sxs-lookup"><span data-stu-id="eef22-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="eef22-345">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能会泄漏机密。</span><span class="sxs-lookup"><span data-stu-id="eef22-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="eef22-346">强烈建议在生产中禁用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="eef22-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="eef22-347">请仔细查看 `UseStaticFiles` 或 `UseDirectoryBrowser` 启用了哪些目录。</span><span class="sxs-lookup"><span data-stu-id="eef22-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="eef22-348">整个目录及其子目录均可公开访问。</span><span class="sxs-lookup"><span data-stu-id="eef22-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="eef22-349">将适合公开的文件存储在专用目录中，如 \<content_root>/wwwroot。</span><span class="sxs-lookup"><span data-stu-id="eef22-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="eef22-350">将这些文件与 MVC 视图、Razor 页面（仅限 2.x）和配置文件等分开。</span><span class="sxs-lookup"><span data-stu-id="eef22-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="eef22-351">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公开的内容的 URL 受大小写和基础文件系统字符限制的影响。</span><span class="sxs-lookup"><span data-stu-id="eef22-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="eef22-352">例如，Windows 不区分大小写 &mdash; macOS 和 Linux 却要区分。</span><span class="sxs-lookup"><span data-stu-id="eef22-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="eef22-353">托管于 IIS 中的 ASP.NET Core 应用使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将所有请求转发到应用，包括静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="eef22-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="eef22-354">未使用 IIS 静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="eef22-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="eef22-355">在模块处理请求前，处理程序没有机会处理请求。</span><span class="sxs-lookup"><span data-stu-id="eef22-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="eef22-356">在 IIS Manager 中完成以下步骤，删除服务器或网站级别的 IIS 静态文件处理程序：</span><span class="sxs-lookup"><span data-stu-id="eef22-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="eef22-357">转到“模块”功能。</span><span class="sxs-lookup"><span data-stu-id="eef22-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="eef22-358">在列表中选择 StaticFileModule。</span><span class="sxs-lookup"><span data-stu-id="eef22-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="eef22-359">单击“操作”侧栏中的“删除” 。</span><span class="sxs-lookup"><span data-stu-id="eef22-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="eef22-360">如果启用了 IIS 静态文件处理程序且 ASP.NET Core 模块配置不正确，则会提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="eef22-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="eef22-361">例如，如果未部署 web.config 文件，则会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="eef22-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="eef22-362">将代码文件（包括 .cs 和 .cshtml ）放在应用项目的 [Web 根目录](xref:fundamentals/index#web-root)之外 。</span><span class="sxs-lookup"><span data-stu-id="eef22-362">Place code files (including *.cs* and *.cshtml* ) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="eef22-363">这样就在应用的客户端内容和基于服务器的代码间创建了逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="eef22-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="eef22-364">可以防止服务器端代码泄漏。</span><span class="sxs-lookup"><span data-stu-id="eef22-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eef22-365">其他资源</span><span class="sxs-lookup"><span data-stu-id="eef22-365">Additional resources</span></span>

* [<span data-ttu-id="eef22-366">中间件</span><span class="sxs-lookup"><span data-stu-id="eef22-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="eef22-367">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="eef22-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
