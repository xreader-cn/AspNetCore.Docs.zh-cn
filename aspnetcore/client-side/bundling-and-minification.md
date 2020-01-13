---
title: ASP.NET Core 中的捆绑和缩小静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩减技术优化 ASP.NET Core web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803274"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="7ddb0-103">ASP.NET Core 中的捆绑和缩小静态资产</span><span class="sxs-lookup"><span data-stu-id="7ddb0-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="7ddb0-104">作者： [Scott Addie](https://twitter.com/Scott_Addie)和[David 松树](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="7ddb0-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="7ddb0-105">本文介绍了应用绑定和缩减，包括如何使用 ASP.NET Core web apps 使用这些功能的好处。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="7ddb0-106">什么是绑定和缩减</span><span class="sxs-lookup"><span data-stu-id="7ddb0-106">What is bundling and minification</span></span>

<span data-ttu-id="7ddb0-107">绑定和缩减是可以在 web 应用中应用的两个不同的性能优化。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="7ddb0-108">绑定和缩减一起使用，可减少服务器请求数并减小请求的静态资产的大小，从而提高性能。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="7ddb0-109">绑定和缩减主要改善第一页请求加载时间。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="7ddb0-110">请求网页后，浏览器会缓存静态资产（JavaScript、CSS 和图像）。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="7ddb0-111">因此，当在同一站点上请求相同的资源时，绑定和缩减不会提高性能。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="7ddb0-112">如果未在资产上正确设置 expires 标头，且未使用捆绑和缩减，则浏览器的新鲜度试探法会在几天后将资产过期。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="7ddb0-113">此外，浏览器还需要对每个资产进行验证请求。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="7ddb0-114">在这种情况下，绑定和缩减在第一次请求页面后仍能改善性能。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="7ddb0-115">捆绑</span><span class="sxs-lookup"><span data-stu-id="7ddb0-115">Bundling</span></span>

<span data-ttu-id="7ddb0-116">捆绑将多个文件合并到单个文件中。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="7ddb0-117">绑定可减少呈现 web 资产（如网页）所需的服务器请求数。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="7ddb0-118">可以专门为 CSS、JavaScript 等创建任意数量的单独包。文件越少，从浏览器到服务器的 HTTP 请求或提供应用程序的服务就会减少。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="7ddb0-119">这会提高第一页的加载性能。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="7ddb0-120">缩减</span><span class="sxs-lookup"><span data-stu-id="7ddb0-120">Minification</span></span>

<span data-ttu-id="7ddb0-121">缩减从代码中删除不必要的字符，而不更改功能。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="7ddb0-122">因此，请求的资产（如 CSS、图像和 JavaScript 文件）的大小大幅减小。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="7ddb0-123">缩减的常见副作用包括将变量名称缩短为一个字符、删除注释和不必要的空格。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="7ddb0-124">请考虑以下 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="7ddb0-125">缩减将函数降到了以下内容：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="7ddb0-126">除了删除注释和不必要的空格外，还会将以下参数和变量名称重命名为：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="7ddb0-127">原始</span><span class="sxs-lookup"><span data-stu-id="7ddb0-127">Original</span></span> | <span data-ttu-id="7ddb0-128">重命名</span><span class="sxs-lookup"><span data-stu-id="7ddb0-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="7ddb0-129">捆绑和缩减的影响</span><span class="sxs-lookup"><span data-stu-id="7ddb0-129">Impact of bundling and minification</span></span>

<span data-ttu-id="7ddb0-130">下表概述了单独加载资产与使用绑定和缩减之间的差异：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="7ddb0-131">操作</span><span class="sxs-lookup"><span data-stu-id="7ddb0-131">Action</span></span> | <span data-ttu-id="7ddb0-132">带有 B/M 的</span><span class="sxs-lookup"><span data-stu-id="7ddb0-132">With B/M</span></span> | <span data-ttu-id="7ddb0-133">无 B/M</span><span class="sxs-lookup"><span data-stu-id="7ddb0-133">Without B/M</span></span> | <span data-ttu-id="7ddb0-134">更改</span><span class="sxs-lookup"><span data-stu-id="7ddb0-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="7ddb0-135">文件请求</span><span class="sxs-lookup"><span data-stu-id="7ddb0-135">File Requests</span></span>  | <span data-ttu-id="7ddb0-136">7</span><span class="sxs-lookup"><span data-stu-id="7ddb0-136">7</span></span>   | <span data-ttu-id="7ddb0-137">18</span><span class="sxs-lookup"><span data-stu-id="7ddb0-137">18</span></span>     | <span data-ttu-id="7ddb0-138">157%</span><span class="sxs-lookup"><span data-stu-id="7ddb0-138">157%</span></span>
<span data-ttu-id="7ddb0-139">已传输 KB</span><span class="sxs-lookup"><span data-stu-id="7ddb0-139">KB Transferred</span></span> | <span data-ttu-id="7ddb0-140">156</span><span class="sxs-lookup"><span data-stu-id="7ddb0-140">156</span></span> | <span data-ttu-id="7ddb0-141">264.68</span><span class="sxs-lookup"><span data-stu-id="7ddb0-141">264.68</span></span> | <span data-ttu-id="7ddb0-142">70%</span><span class="sxs-lookup"><span data-stu-id="7ddb0-142">70%</span></span>
<span data-ttu-id="7ddb0-143">加载时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="7ddb0-143">Load Time (ms)</span></span> | <span data-ttu-id="7ddb0-144">885</span><span class="sxs-lookup"><span data-stu-id="7ddb0-144">885</span></span> | <span data-ttu-id="7ddb0-145">2360</span><span class="sxs-lookup"><span data-stu-id="7ddb0-145">2360</span></span>   | <span data-ttu-id="7ddb0-146">167%</span><span class="sxs-lookup"><span data-stu-id="7ddb0-146">167%</span></span>

<span data-ttu-id="7ddb0-147">对于 HTTP 请求标头，浏览器非常详细。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="7ddb0-148">绑定的字节总数指标明显减少了绑定的时间。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="7ddb0-149">加载时间显示了显著改进，但本示例在本地运行。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="7ddb0-150">将捆绑与缩减与通过网络传输的资产结合使用时，可实现更高的性能提升。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="7ddb0-151">选择捆绑和缩减策略</span><span class="sxs-lookup"><span data-stu-id="7ddb0-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="7ddb0-152">MVC 和 Razor Pages 项目模板提供了一种现成的解决方案，可用于缩减和 JSON 配置文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="7ddb0-153">第三方工具（如[Grunt](xref:client-side/using-grunt)任务运行程序）以更复杂的方式完成相同的任务。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="7ddb0-154">当开发工作流需要处理超过绑定和缩减&mdash;如 linting 和图像优化）时，第三方工具非常合适。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="7ddb0-155">通过使用设计时绑定和缩减，缩小文件是在应用部署之前创建的。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="7ddb0-156">在部署之前绑定和缩小提供了降低服务器负载的优点。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="7ddb0-157">但是，必须认识到，设计时绑定和缩减会增加生成的复杂性，并且仅适用于静态文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7ddb0-158">配置捆绑和缩减</span><span class="sxs-lookup"><span data-stu-id="7ddb0-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="7ddb0-159">在 ASP.NET Core 2.0 或更早版本中，MVC 和 Razor Pages 项目模板提供了一个*bundleconfig*配置文件，该文件定义每个绑定的选项：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7ddb0-160">在 ASP.NET Core 2.1 或更高版本中，将名为*bundleconfig*的新 JSON 文件添加到 MVC 或 Razor Pages 项目根。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="7ddb0-161">在该文件中包含以下 JSON 作为起始点：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="7ddb0-162">*Bundleconfig*文件定义每个绑定的选项。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="7ddb0-163">在前面的示例中，为自定义 JavaScript （*wwwroot/js/node.js*）和样式表（*wwwroot/css/站点导航*）文件定义了单个捆绑配置。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="7ddb0-164">配置选项包括：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-164">Configuration options include:</span></span>

* <span data-ttu-id="7ddb0-165">`outputFileName`：要输出的绑定文件的名称。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="7ddb0-166">可以包含*bundleconfig*文件中的相对路径。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="7ddb0-167">（必需）</span><span class="sxs-lookup"><span data-stu-id="7ddb0-167">**required**</span></span>
* <span data-ttu-id="7ddb0-168">`inputFiles`：要捆绑在一起的文件的数组。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="7ddb0-169">这些是配置文件的相对路径。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="7ddb0-170">**可选**，\* 空值将导致空的输出文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="7ddb0-171">支持[通配](https://www.tldp.org/LDP/abs/html/globbingref.html)模式。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="7ddb0-172">`minify`：输出类型的缩减选项。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="7ddb0-173">**可选**，*默认值-`minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="7ddb0-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="7ddb0-174">每个输出文件类型都有配置选项。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="7ddb0-175">CSS 缩小器</span><span class="sxs-lookup"><span data-stu-id="7ddb0-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="7ddb0-176">JavaScript 缩小器</span><span class="sxs-lookup"><span data-stu-id="7ddb0-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="7ddb0-177">HTML 缩小器</span><span class="sxs-lookup"><span data-stu-id="7ddb0-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="7ddb0-178">`includeInProject`：指示是否将生成的文件添加到项目文件的标记。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="7ddb0-179">**可选**，*默认值为 false*</span><span class="sxs-lookup"><span data-stu-id="7ddb0-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="7ddb0-180">`sourceMap`：标记，指示是否为绑定的文件生成源映射。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="7ddb0-181">**可选**，*默认值为 false*</span><span class="sxs-lookup"><span data-stu-id="7ddb0-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="7ddb0-182">`sourceMapRootPath`：用于存储生成的源映射文件的根路径。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="7ddb0-183">绑定和缩减的生成时执行</span><span class="sxs-lookup"><span data-stu-id="7ddb0-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="7ddb0-184">[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet 包允许在生成时执行绑定和缩减。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="7ddb0-185">包注入在生成和清理时间运行的[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="7ddb0-186">*Bundleconfig*文件由生成过程进行分析，以便基于定义的配置生成输出文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="7ddb0-187">BuildBundlerMinifier 属于 GitHub 上的社区驱动项目，Microsoft 不提供支持。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="7ddb0-188">应在[此处](https://github.com/madskristensen/BundlerMinifier/issues)归档问题。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7ddb0-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ddb0-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7ddb0-190">将*BuildBundlerMinifier*包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="7ddb0-191">生成此项目。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-191">Build the project.</span></span> <span data-ttu-id="7ddb0-192">"输出" 窗口中将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-192">The following appears in the Output window:</span></span>

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

<span data-ttu-id="7ddb0-193">清理项目。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-193">Clean the project.</span></span> <span data-ttu-id="7ddb0-194">"输出" 窗口中将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7ddb0-195">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7ddb0-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7ddb0-196">将*BuildBundlerMinifier*包添加到项目：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="7ddb0-197">如果使用 ASP.NET Core 1.x，还原新添加的包：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="7ddb0-198">生成项目：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="7ddb0-199">将显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="7ddb0-200">清理项目：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="7ddb0-201">将显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="7ddb0-202">绑定和缩减的即席执行</span><span class="sxs-lookup"><span data-stu-id="7ddb0-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="7ddb0-203">可以在不生成项目的情况下即席运行捆绑和缩减任务。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="7ddb0-204">将[BundlerMinifier](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet 包添加到项目：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="7ddb0-205">BundlerMinifier 属于 GitHub 上的社区驱动项目，Microsoft 不提供支持。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="7ddb0-206">应在[此处](https://github.com/madskristensen/BundlerMinifier/issues)归档问题。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="7ddb0-207">此包扩展以包括.NET Core CLI *dotnet 捆绑*工具。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="7ddb0-208">可以在 "包管理器控制台" （PMC）窗口或命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="7ddb0-209">NuGet 包管理器将依赖项添加到 \* .csproj 文件作为 `<PackageReference />` 节点。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="7ddb0-210">`dotnet bundle`命令注册.NET Core CLI 时，才`<DotNetCliToolReference />`使用节点。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="7ddb0-211">请相应地修改 \* .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="7ddb0-212">向工作流添加文件</span><span class="sxs-lookup"><span data-stu-id="7ddb0-212">Add files to workflow</span></span>

<span data-ttu-id="7ddb0-213">假设添加了其他*自定义 .css*文件的示例类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="7ddb0-214">若要缩小*自定义 css*并将其与*站点* *中的内容*捆绑在一起，请将相对路径添加到*bundleconfig*：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="7ddb0-215">或者，可以使用以下组合模式：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="7ddb0-216">此组合模式匹配所有 CSS 文件，并排除缩小文件模式。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="7ddb0-217">生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-217">Build the application.</span></span> <span data-ttu-id="7ddb0-218">打开 "*网站"。最小 css* ，请注意，*自定义 css*的内容将追加到文件末尾。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="7ddb0-219">基于环境的捆绑和缩减</span><span class="sxs-lookup"><span data-stu-id="7ddb0-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="7ddb0-220">最佳做法是，应在生产环境中使用应用的捆绑文件和缩小文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="7ddb0-221">在开发过程中，原始文件可简化应用程序的调试。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="7ddb0-222">通过在视图中使用[环境标记帮助器](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)来指定要包含在页面中的文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="7ddb0-223">环境标记帮助程序仅在特定[环境](xref:fundamentals/environments)中运行时呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7ddb0-224">以下 `environment` 标记将在 `Development` 环境中运行时呈现未处理的 CSS 文件：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="7ddb0-225">当在非 `Development`环境中运行时，以下 `environment` 标记将呈现捆绑的和缩小的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="7ddb0-226">例如，在 `Production` 或 `Staging` 中运行将触发这些样式表的呈现：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="7ddb0-227">从 Gulp 使用 bundleconfig</span><span class="sxs-lookup"><span data-stu-id="7ddb0-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="7ddb0-228">在某些情况下，应用的绑定和缩减工作流需要额外处理。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="7ddb0-229">示例包括图像优化、缓存清除和 CDN 资产处理。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="7ddb0-230">为了满足这些要求，你可以将绑定和缩减工作流转换为使用 Gulp。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="7ddb0-231">使用捆绑程序 & 缩小器扩展</span><span class="sxs-lookup"><span data-stu-id="7ddb0-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="7ddb0-232">Visual Studio[捆绑程序 & 缩小器](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)扩展处理到 Gulp 的转换。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="7ddb0-233">捆绑程序 & 缩小器扩展属于 Microsoft 不提供支持的 GitHub 上的社区驱动项目。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="7ddb0-234">应在[此处](https://github.com/madskristensen/BundlerMinifier/issues)归档问题。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="7ddb0-235">右键单击解决方案资源管理器中的*bundleconfig*文件，然后选择**捆绑程序 & 缩小器** > **转换为 Gulp ...** ：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![转换为 Gulp 上下文菜单项](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="7ddb0-237">将*gulpfile*和*包*文件添加到项目。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="7ddb0-238">安装 package.json 文件的 `devDependencies` 部分中列出的支持的 [npm](https://www.npmjs.com/) 包。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="7ddb0-239">在 PMC 窗口中运行以下命令，以将 Gulp CLI 作为全局依赖项安装：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="7ddb0-240">*Gulpfile*文件读取输入、输出和设置的*bundleconfig*文件。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="7ddb0-241">手动转换</span><span class="sxs-lookup"><span data-stu-id="7ddb0-241">Convert manually</span></span>

<span data-ttu-id="7ddb0-242">如果 Visual Studio 和/或捆绑程序 & 缩小器扩展不可用，请手动转换。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="7ddb0-243">使用以下 `devDependencies`将*包 json*文件添加到项目根目录：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="7ddb0-244">`gulp-uglify` 模块不支持 ECMAScript （ES） 2015/ES6 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="7ddb0-245">安装[gulp-terser](https://www.npmjs.com/package/gulp-terser)而不是 `gulp-uglify` 来使用 ES2015/ES6 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="7ddb0-246">通过在与 package 相同的级别上运行以下命令来安装依赖项 *。 json*：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="7ddb0-247">安装 Gulp CLI 作为全局依赖项：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="7ddb0-248">将下面的*gulpfile*文件复制到项目根：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="7ddb0-249">运行 Gulp 任务</span><span class="sxs-lookup"><span data-stu-id="7ddb0-249">Run Gulp tasks</span></span>

<span data-ttu-id="7ddb0-250">若要在 Visual Studio 中生成项目之前触发 Gulp 缩减任务，请将以下[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)添加到 \* .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="7ddb0-251">在此示例中，`MyPreCompileTarget` 目标内定义的所有任务在预定义 `Build` 目标之前运行。</span><span class="sxs-lookup"><span data-stu-id="7ddb0-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="7ddb0-252">Visual Studio 的输出窗口中显示类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="7ddb0-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7ddb0-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="7ddb0-253">Additional resources</span></span>

* [<span data-ttu-id="7ddb0-254">使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="7ddb0-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="7ddb0-255">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="7ddb0-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="7ddb0-256">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="7ddb0-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
