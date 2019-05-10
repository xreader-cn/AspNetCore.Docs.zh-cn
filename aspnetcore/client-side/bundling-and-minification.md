---
title: 捆绑和缩小在 ASP.NET Core 中的静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩减技术优化 ASP.NET Core web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/20/2018
uid: client-side/bundling-and-minification
ms.openlocfilehash: 5d5f0aadb7740c9b2b959d12a585cd8c91758ce8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894294"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="cbb77-103">捆绑和缩小在 ASP.NET Core 中的静态资产</span><span class="sxs-lookup"><span data-stu-id="cbb77-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="cbb77-104">通过[Scott Addie](https://twitter.com/Scott_Addie)和[David 松树](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="cbb77-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="cbb77-105">本文介绍了应用绑定和缩减，包括如何使用 ASP.NET Core web apps 使用这些功能的好处。</span><span class="sxs-lookup"><span data-stu-id="cbb77-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="cbb77-106">捆绑和缩小是什么</span><span class="sxs-lookup"><span data-stu-id="cbb77-106">What is bundling and minification</span></span>

<span data-ttu-id="cbb77-107">捆绑和缩小可以应用的 web 应用中的两个明显的性能优化。</span><span class="sxs-lookup"><span data-stu-id="cbb77-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="cbb77-108">一起使用时，捆绑和缩小通过提高性能降低的服务器请求数和减小请求的静态资产的大小。</span><span class="sxs-lookup"><span data-stu-id="cbb77-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="cbb77-109">捆绑和缩小主要提高第一个页面请求加载时间。</span><span class="sxs-lookup"><span data-stu-id="cbb77-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="cbb77-110">一旦请求 web 页，在浏览器缓存静态资产 （JavaScript、 CSS 和图像）。</span><span class="sxs-lookup"><span data-stu-id="cbb77-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="cbb77-111">因此，绑定和缩减不提高性能时请求同一页上或请求同一资产相同站点上的页。</span><span class="sxs-lookup"><span data-stu-id="cbb77-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="cbb77-112">如果过期标头未正确设置对资产并没有使用绑定和缩减，如果浏览器的新鲜度试探法将标记资产过时几天后。</span><span class="sxs-lookup"><span data-stu-id="cbb77-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="cbb77-113">此外，在浏览器为每个资产需要验证请求。</span><span class="sxs-lookup"><span data-stu-id="cbb77-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="cbb77-114">在这种情况下，绑定和缩减提供性能改进甚至后第一个页面请求。</span><span class="sxs-lookup"><span data-stu-id="cbb77-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="cbb77-115">捆绑</span><span class="sxs-lookup"><span data-stu-id="cbb77-115">Bundling</span></span>

<span data-ttu-id="cbb77-116">捆绑将多个文件合并到单个文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="cbb77-117">绑定可减少所需呈现 web 资产，例如网页的服务器请求的数量。</span><span class="sxs-lookup"><span data-stu-id="cbb77-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="cbb77-118">专门为 CSS、 JavaScript 等，可以创建任意数量的单独的捆绑包。更少的文件意味着较少的 HTTP 请求从浏览器到服务器或提供你的应用程序的服务。</span><span class="sxs-lookup"><span data-stu-id="cbb77-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="cbb77-119">这会改进了第一个页面加载性能。</span><span class="sxs-lookup"><span data-stu-id="cbb77-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="cbb77-120">缩小</span><span class="sxs-lookup"><span data-stu-id="cbb77-120">Minification</span></span>

<span data-ttu-id="cbb77-121">缩减而无需更改功能从代码中删除不必要的字符。</span><span class="sxs-lookup"><span data-stu-id="cbb77-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="cbb77-122">结果是很大大小减少请求的资产 （如 CSS、 图像和 JavaScript 文件）。</span><span class="sxs-lookup"><span data-stu-id="cbb77-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="cbb77-123">缩减的常见副作用包括缩短为一个字符的变量名称和删除注释和不必要的空格。</span><span class="sxs-lookup"><span data-stu-id="cbb77-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="cbb77-124">请考虑以下的 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="cbb77-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="cbb77-125">缩小简化为以下函数：</span><span class="sxs-lookup"><span data-stu-id="cbb77-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="cbb77-126">除了删除注释和不必要的空格，以下参数和变量名称已重命名，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cbb77-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="cbb77-127">原始</span><span class="sxs-lookup"><span data-stu-id="cbb77-127">Original</span></span> | <span data-ttu-id="cbb77-128">重命名</span><span class="sxs-lookup"><span data-stu-id="cbb77-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="cbb77-129">捆绑和缩减的影响</span><span class="sxs-lookup"><span data-stu-id="cbb77-129">Impact of bundling and minification</span></span>

<span data-ttu-id="cbb77-130">下表概述了单独加载资产与使用绑定和缩减之间的差异：</span><span class="sxs-lookup"><span data-stu-id="cbb77-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="cbb77-131">操作</span><span class="sxs-lookup"><span data-stu-id="cbb77-131">Action</span></span> | <span data-ttu-id="cbb77-132">B/m</span><span class="sxs-lookup"><span data-stu-id="cbb77-132">With B/M</span></span> | <span data-ttu-id="cbb77-133">Without B/M</span><span class="sxs-lookup"><span data-stu-id="cbb77-133">Without B/M</span></span> | <span data-ttu-id="cbb77-134">更改</span><span class="sxs-lookup"><span data-stu-id="cbb77-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="cbb77-135">文件请求</span><span class="sxs-lookup"><span data-stu-id="cbb77-135">File Requests</span></span>  | <span data-ttu-id="cbb77-136">7</span><span class="sxs-lookup"><span data-stu-id="cbb77-136">7</span></span>   | <span data-ttu-id="cbb77-137">18</span><span class="sxs-lookup"><span data-stu-id="cbb77-137">18</span></span>     | <span data-ttu-id="cbb77-138">157%</span><span class="sxs-lookup"><span data-stu-id="cbb77-138">157%</span></span>
<span data-ttu-id="cbb77-139">传输的 KB</span><span class="sxs-lookup"><span data-stu-id="cbb77-139">KB Transferred</span></span> | <span data-ttu-id="cbb77-140">156</span><span class="sxs-lookup"><span data-stu-id="cbb77-140">156</span></span> | <span data-ttu-id="cbb77-141">264.68</span><span class="sxs-lookup"><span data-stu-id="cbb77-141">264.68</span></span> | <span data-ttu-id="cbb77-142">70%</span><span class="sxs-lookup"><span data-stu-id="cbb77-142">70%</span></span>
<span data-ttu-id="cbb77-143">加载时间 （毫秒）</span><span class="sxs-lookup"><span data-stu-id="cbb77-143">Load Time (ms)</span></span> | <span data-ttu-id="cbb77-144">885</span><span class="sxs-lookup"><span data-stu-id="cbb77-144">885</span></span> | <span data-ttu-id="cbb77-145">2360</span><span class="sxs-lookup"><span data-stu-id="cbb77-145">2360</span></span>   | <span data-ttu-id="cbb77-146">167%</span><span class="sxs-lookup"><span data-stu-id="cbb77-146">167%</span></span>

<span data-ttu-id="cbb77-147">浏览器是相当冗长关于 HTTP 请求标头。</span><span class="sxs-lookup"><span data-stu-id="cbb77-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="cbb77-148">总字节数发送指标时将看到显著降低。</span><span class="sxs-lookup"><span data-stu-id="cbb77-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="cbb77-149">加载时间显示了重大改进，但此示例在本地运行。</span><span class="sxs-lookup"><span data-stu-id="cbb77-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="cbb77-150">与资产使用捆绑和缩小通过网络传输时实现更高的性能提升。</span><span class="sxs-lookup"><span data-stu-id="cbb77-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="cbb77-151">选择捆绑和缩减策略</span><span class="sxs-lookup"><span data-stu-id="cbb77-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="cbb77-152">MVC 和 Razor 页面项目模板提供开箱解决方案捆绑和缩小包含 JSON 配置文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="cbb77-153">第三方工具，如[Gulp](xref:client-side/using-gulp)并[Grunt](xref:client-side/using-grunt)任务运行程序中，完成相同的任务具有更多的复杂性。</span><span class="sxs-lookup"><span data-stu-id="cbb77-153">Third-party tools, such as the [Gulp](xref:client-side/using-gulp) and [Grunt](xref:client-side/using-grunt) task runners, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="cbb77-154">第三方工具是理想的选择时开发工作流需要处理超出捆绑和缩小&mdash;linting 和映像优化等。</span><span class="sxs-lookup"><span data-stu-id="cbb77-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="cbb77-155">通过使用设计时绑定和缩减，在应用程序的部署前创建缩小化的文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="cbb77-156">捆绑和缩小在部署前提供减少的服务器负载的优点。</span><span class="sxs-lookup"><span data-stu-id="cbb77-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="cbb77-157">但是，务必要识别该设计时捆绑和缩小会增大复杂性，生成和仅适用于静态文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="cbb77-158">配置捆绑和缩减</span><span class="sxs-lookup"><span data-stu-id="cbb77-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="cbb77-159">MVC 和 Razor 页面项目模板提供了在 ASP.NET Core 2.0 或更早*bundleconfig.json*定义每个捆绑包的选项的配置文件：</span><span class="sxs-lookup"><span data-stu-id="cbb77-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="cbb77-160">在 ASP.NET Core 2.1 或更高版本，添加一个新的 JSON 文件，名为*bundleconfig.json*，与 MVC 或 Razor 页面项目根目录。</span><span class="sxs-lookup"><span data-stu-id="cbb77-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="cbb77-161">作为起始点，在该文件中包含以下 JSON:</span><span class="sxs-lookup"><span data-stu-id="cbb77-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="cbb77-162">*Bundleconfig.json*文件定义每个捆绑包的选项。</span><span class="sxs-lookup"><span data-stu-id="cbb77-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="cbb77-163">在上述示例中，一个捆绑包配置为定义了自定义 JavaScript (*wwwroot/js/site.js*) 和样式表 (*wwwroot/css/site.css*) 文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="cbb77-164">配置选项包括：</span><span class="sxs-lookup"><span data-stu-id="cbb77-164">Configuration options include:</span></span>

* <span data-ttu-id="cbb77-165">`outputFileName`：要输出的捆绑包文件的名称。</span><span class="sxs-lookup"><span data-stu-id="cbb77-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="cbb77-166">可以包含中的相对路径*bundleconfig.json*文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="cbb77-167">**required**</span><span class="sxs-lookup"><span data-stu-id="cbb77-167">**required**</span></span>
* <span data-ttu-id="cbb77-168">`inputFiles`：要捆绑在一起的文件的数组。</span><span class="sxs-lookup"><span data-stu-id="cbb77-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="cbb77-169">这些是配置文件的相对路径。</span><span class="sxs-lookup"><span data-stu-id="cbb77-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="cbb77-170">**可选**，\* 空值会导致空的输出文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="cbb77-171">[通配](http://www.tldp.org/LDP/abs/html/globbingref.html)支持模式。</span><span class="sxs-lookup"><span data-stu-id="cbb77-171">[globbing](http://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="cbb77-172">`minify`：输出类型缩小选项。</span><span class="sxs-lookup"><span data-stu-id="cbb77-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="cbb77-173">**可选**，*默认值- `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="cbb77-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="cbb77-174">每个输出文件类型提供了配置选项。</span><span class="sxs-lookup"><span data-stu-id="cbb77-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="cbb77-175">CSS 缩小器</span><span class="sxs-lookup"><span data-stu-id="cbb77-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="cbb77-176">JavaScript 缩减程序</span><span class="sxs-lookup"><span data-stu-id="cbb77-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="cbb77-177">HTML 缩小器</span><span class="sxs-lookup"><span data-stu-id="cbb77-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="cbb77-178">`includeInProject`：指示是否将生成的文件添加到项目文件的标记。</span><span class="sxs-lookup"><span data-stu-id="cbb77-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="cbb77-179">**可选**，*默认值-false*</span><span class="sxs-lookup"><span data-stu-id="cbb77-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="cbb77-180">`sourceMap`：指示是否生成将捆绑的文件的源映射的标记。</span><span class="sxs-lookup"><span data-stu-id="cbb77-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="cbb77-181">**可选**，*默认值-false*</span><span class="sxs-lookup"><span data-stu-id="cbb77-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="cbb77-182">`sourceMapRootPath`：用于存储生成的源映射文件的根路径。</span><span class="sxs-lookup"><span data-stu-id="cbb77-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="cbb77-183">生成时执行的捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="cbb77-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="cbb77-184">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet 程序包可执行的捆绑和缩小在生成时启用。</span><span class="sxs-lookup"><span data-stu-id="cbb77-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="cbb77-185">包将注入[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)其在生成和清理的时间运行。</span><span class="sxs-lookup"><span data-stu-id="cbb77-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="cbb77-186">*Bundleconfig.json*文件分析的生成过程以生成基于定义的配置的输出文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="cbb77-187">BuildBundlerMinifier 属于 Microsoft 为其提供任何支持的 GitHub 上的社区驱动项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="cbb77-188">问题应将归档[此处](https://github.com/madskristensen/BundlerMinifier/issues)。</span><span class="sxs-lookup"><span data-stu-id="cbb77-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cbb77-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cbb77-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cbb77-190">添加*BuildBundlerMinifier*包到你的项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="cbb77-191">生成项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-191">Build the project.</span></span> <span data-ttu-id="cbb77-192">将显示以下输出窗口中：</span><span class="sxs-lookup"><span data-stu-id="cbb77-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="cbb77-193">清除该项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-193">Clean the project.</span></span> <span data-ttu-id="cbb77-194">将显示以下输出窗口中：</span><span class="sxs-lookup"><span data-stu-id="cbb77-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="cbb77-195">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cbb77-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cbb77-196">添加*BuildBundlerMinifier*包到你的项目：</span><span class="sxs-lookup"><span data-stu-id="cbb77-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```console
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="cbb77-197">如果使用 ASP.NET Core 1.x，还原新添加的包：</span><span class="sxs-lookup"><span data-stu-id="cbb77-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```console
dotnet restore
```

<span data-ttu-id="cbb77-198">生成项目：</span><span class="sxs-lookup"><span data-stu-id="cbb77-198">Build the project:</span></span>

```console
dotnet build
```

<span data-ttu-id="cbb77-199">将显示以下：</span><span class="sxs-lookup"><span data-stu-id="cbb77-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="cbb77-200">清除该项目：</span><span class="sxs-lookup"><span data-stu-id="cbb77-200">Clean the project:</span></span>

```console
dotnet clean
```

<span data-ttu-id="cbb77-201">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="cbb77-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="cbb77-202">捆绑和缩小的即席执行</span><span class="sxs-lookup"><span data-stu-id="cbb77-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="cbb77-203">很可能在某些特殊情况，运行绑定和缩减任务，而无需生成项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="cbb77-204">添加[BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/)到你的项目的 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="cbb77-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="cbb77-205">BundlerMinifier.Core 属于 Microsoft 为其提供任何支持的 GitHub 上的社区驱动项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="cbb77-206">问题应将归档[此处](https://github.com/madskristensen/BundlerMinifier/issues)。</span><span class="sxs-lookup"><span data-stu-id="cbb77-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="cbb77-207">此包扩展以包括.NET Core CLI *dotnet 捆绑*工具。</span><span class="sxs-lookup"><span data-stu-id="cbb77-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="cbb77-208">在包管理器控制台 (PMC) 窗口或命令行界面中，可以执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cbb77-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```console
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="cbb77-209">NuGet 包管理器将依赖项添加到 \*.csproj 文件作为`<PackageReference />`节点。</span><span class="sxs-lookup"><span data-stu-id="cbb77-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="cbb77-210">`dotnet bundle`命令注册.NET Core CLI 时，才`<DotNetCliToolReference />`使用节点。</span><span class="sxs-lookup"><span data-stu-id="cbb77-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="cbb77-211">相应地修改 \*.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="cbb77-212">将文件添加到工作流</span><span class="sxs-lookup"><span data-stu-id="cbb77-212">Add files to workflow</span></span>

<span data-ttu-id="cbb77-213">在其中一个示例，请考虑额外*custom.css*与下面类似的添加文件：</span><span class="sxs-lookup"><span data-stu-id="cbb77-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="cbb77-214">若要缩小*custom.css*并将其与捆绑*site.css*到*site.min.css*文件中，添加的相对路径*bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="cbb77-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="cbb77-215">或者，可使用以下通配模式：</span><span class="sxs-lookup"><span data-stu-id="cbb77-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> <span data-ttu-id="cbb77-216">此通配模式匹配所有 CSS 文件，并不包括缩小的文件模式。</span><span class="sxs-lookup"><span data-stu-id="cbb77-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="cbb77-217">生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="cbb77-217">Build the application.</span></span> <span data-ttu-id="cbb77-218">打开*site.min.css* ，并注意的内容*custom.css*追加到文件末尾。</span><span class="sxs-lookup"><span data-stu-id="cbb77-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="cbb77-219">基于环境的捆绑和缩减</span><span class="sxs-lookup"><span data-stu-id="cbb77-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="cbb77-220">作为最佳做法，应在生产环境中使用你的应用的捆绑和缩小化文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="cbb77-221">在开发期间，为方便调试的应用程序将使原始文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="cbb77-222">指定要使用包括在页面中的文件[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)在视图中。</span><span class="sxs-lookup"><span data-stu-id="cbb77-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="cbb77-223">环境标记帮助程序中特定于运行时只呈现其内容[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="cbb77-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="cbb77-224">以下`environment`标记将转换的未处理的 CSS 文件在运行时`Development`环境：</span><span class="sxs-lookup"><span data-stu-id="cbb77-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="cbb77-225">以下`environment`标记呈现的捆绑和缩小 CSS 文件，而不在环境中运行时`Development`。</span><span class="sxs-lookup"><span data-stu-id="cbb77-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="cbb77-226">例如，在运行`Production`或`Staging`触发这些样式表的呈现：</span><span class="sxs-lookup"><span data-stu-id="cbb77-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="cbb77-227">使用 Gulp 从 bundleconfig.json</span><span class="sxs-lookup"><span data-stu-id="cbb77-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="cbb77-228">有些情况下在其中应用的捆绑和缩小工作流需要进行其他处理。</span><span class="sxs-lookup"><span data-stu-id="cbb77-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="cbb77-229">示例包括图像优化、 清除缓存，和 CDN 资产处理。</span><span class="sxs-lookup"><span data-stu-id="cbb77-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="cbb77-230">若要满足这些要求，可以将转换使用 Gulp 的捆绑和缩小工作流。</span><span class="sxs-lookup"><span data-stu-id="cbb77-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="cbb77-231">使用捆绑程序和缩小器扩展</span><span class="sxs-lookup"><span data-stu-id="cbb77-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="cbb77-232">Visual Studio[捆绑程序和缩小器](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)扩展处理到 Gulp 的转换。</span><span class="sxs-lookup"><span data-stu-id="cbb77-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="cbb77-233">捆绑程序和缩小器扩展属于 Microsoft 为其提供任何支持的 GitHub 上的社区驱动项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="cbb77-234">问题应将归档[此处](https://github.com/madskristensen/BundlerMinifier/issues)。</span><span class="sxs-lookup"><span data-stu-id="cbb77-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="cbb77-235">右键单击*bundleconfig.json*文件在解决方案资源管理器中，然后选择**捆绑程序和缩小器** > **转换 Gulp...**:</span><span class="sxs-lookup"><span data-stu-id="cbb77-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![将转换到 Gulp 上下文菜单项](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="cbb77-237">*Gulpfile.js*并*package.json*文件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="cbb77-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="cbb77-238">支持[npm](https://www.npmjs.com/)中列出的包*package.json*文件的`devDependencies`部分安装。</span><span class="sxs-lookup"><span data-stu-id="cbb77-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="cbb77-239">若要安装 Gulp CLI 作为全局依赖项在 PMC 窗口中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cbb77-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="cbb77-240">*Gulpfile.js*文件读取*bundleconfig.json*输入、 输出和设置的文件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="cbb77-241">手动转换</span><span class="sxs-lookup"><span data-stu-id="cbb77-241">Convert manually</span></span>

<span data-ttu-id="cbb77-242">如果 Visual Studio 和/或捆绑程序和缩小器扩展插件不可用，手动转换。</span><span class="sxs-lookup"><span data-stu-id="cbb77-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="cbb77-243">添加*package.json*文件中的，使用以下`devDependencies`，到项目根目录：</span><span class="sxs-lookup"><span data-stu-id="cbb77-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="cbb77-244">通过与同一级别中运行以下命令安装依赖项*package.json*:</span><span class="sxs-lookup"><span data-stu-id="cbb77-244">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="cbb77-245">为全局的依赖项安装 Gulp CLI:</span><span class="sxs-lookup"><span data-stu-id="cbb77-245">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="cbb77-246">复制*gulpfile.js*到项目根文件如下：</span><span class="sxs-lookup"><span data-stu-id="cbb77-246">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="cbb77-247">运行 Gulp 任务</span><span class="sxs-lookup"><span data-stu-id="cbb77-247">Run Gulp tasks</span></span>

<span data-ttu-id="cbb77-248">若要触发 Gulp 缩小任务之前在 Visual Studio 中生成项目，添加以下[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)到 \*.csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="cbb77-248">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="cbb77-249">在此示例中定义的任何任务`MyPreCompileTarget`目标运行之前在预定义`Build`目标。</span><span class="sxs-lookup"><span data-stu-id="cbb77-249">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="cbb77-250">在 Visual Studio 输出窗口中显示类似于以下输出：</span><span class="sxs-lookup"><span data-stu-id="cbb77-250">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="cbb77-251">或者，可能会使用 Visual Studio 的任务运行程序资源管理器来将 Gulp 任务绑定到特定的 Visual Studio 事件。</span><span class="sxs-lookup"><span data-stu-id="cbb77-251">Alternatively, Visual Studio's Task Runner Explorer may be used to bind Gulp tasks to specific Visual Studio events.</span></span> <span data-ttu-id="cbb77-252">请参阅[正在运行的默认任务](xref:client-side/using-gulp#running-default-tasks)有关执行此操作的说明。</span><span class="sxs-lookup"><span data-stu-id="cbb77-252">See [Running default tasks](xref:client-side/using-gulp#running-default-tasks) for instructions on doing that.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cbb77-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="cbb77-253">Additional resources</span></span>

* [<span data-ttu-id="cbb77-254">使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="cbb77-254">Use Gulp</span></span>](xref:client-side/using-gulp)
* [<span data-ttu-id="cbb77-255">使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="cbb77-255">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="cbb77-256">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="cbb77-256">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="cbb77-257">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="cbb77-257">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
