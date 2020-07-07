---
title: ASP.NET Core 中的捆绑和缩小静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩小技术优化 ASP.NET Core Web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: de7c155189008e1f78bfb1eba062fcc86f9e4839
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401904"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="312c8-103">ASP.NET Core 中的捆绑和缩小静态资产</span><span class="sxs-lookup"><span data-stu-id="312c8-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="312c8-104">作者：[Scott Addie](https://twitter.com/Scott_Addie) 和 [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="312c8-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="312c8-105">本文介绍应用捆绑和缩小的好处，包括如何在 ASP.NET Core Web 应用中使用这些功能。</span><span class="sxs-lookup"><span data-stu-id="312c8-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="312c8-106">什么是捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="312c8-106">What is bundling and minification</span></span>

<span data-ttu-id="312c8-107">捆绑和缩小是可以在 Web 应用中应用的两个不同的性能优化。</span><span class="sxs-lookup"><span data-stu-id="312c8-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="312c8-108">捆绑和缩小一起使用，可减少服务器的请求数并减小请求的静态资产的大小，从而提高性能。</span><span class="sxs-lookup"><span data-stu-id="312c8-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="312c8-109">捆绑和缩小主要缩短第一个页面请求加载时间。</span><span class="sxs-lookup"><span data-stu-id="312c8-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="312c8-110">请求网页后，浏览器会缓存静态资产（JavaScript、CSS 和图像）。</span><span class="sxs-lookup"><span data-stu-id="312c8-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="312c8-111">因此，在请求相同资产的同一站点上请求相同的一个或多个页面时，捆绑和缩小不会提高性能。</span><span class="sxs-lookup"><span data-stu-id="312c8-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="312c8-112">如果未在资产上正确设置 expires 标头，且未使用捆绑和缩小，则浏览器的新鲜度启发会在几天后将资产标记为过期。</span><span class="sxs-lookup"><span data-stu-id="312c8-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="312c8-113">此外，浏览器还需要对每个资产进行验证请求。</span><span class="sxs-lookup"><span data-stu-id="312c8-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="312c8-114">在这种情况下，即使在第一个页面请求后，捆绑和缩小仍能提高性能。</span><span class="sxs-lookup"><span data-stu-id="312c8-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="312c8-115">捆绑</span><span class="sxs-lookup"><span data-stu-id="312c8-115">Bundling</span></span>

<span data-ttu-id="312c8-116">捆绑将多个文件合并到单个文件中。</span><span class="sxs-lookup"><span data-stu-id="312c8-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="312c8-117">捆绑可减少呈现 Web 资产（如网页）所需的服务器请求数。</span><span class="sxs-lookup"><span data-stu-id="312c8-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="312c8-118">可以专门为 CSS、JavaScript 等创建任意数量的单个捆绑。文件越少，从浏览器到服务器或从提供应用程序的服务的 HTTP 请求就越少。</span><span class="sxs-lookup"><span data-stu-id="312c8-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="312c8-119">这会提高第一页加载性能。</span><span class="sxs-lookup"><span data-stu-id="312c8-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="312c8-120">缩小</span><span class="sxs-lookup"><span data-stu-id="312c8-120">Minification</span></span>

<span data-ttu-id="312c8-121">缩小在不更改功能的情况下从代码中删除不必要的字符。</span><span class="sxs-lookup"><span data-stu-id="312c8-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="312c8-122">因此，请求的资产（如 CSS、图像和 JavaScript 文件）的大小大幅减小。</span><span class="sxs-lookup"><span data-stu-id="312c8-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="312c8-123">缩小的常见副作用包括将变量名称缩短为一个字符、删除注释和不必要的空格。</span><span class="sxs-lookup"><span data-stu-id="312c8-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="312c8-124">考虑以下 JavaScript 函数：</span><span class="sxs-lookup"><span data-stu-id="312c8-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="312c8-125">缩小将函数缩减为以下内容：</span><span class="sxs-lookup"><span data-stu-id="312c8-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="312c8-126">除了删除注释和不必要的空格外，还进行了以下参数和变量名称重命名：</span><span class="sxs-lookup"><span data-stu-id="312c8-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="312c8-127">原始</span><span class="sxs-lookup"><span data-stu-id="312c8-127">Original</span></span> | <span data-ttu-id="312c8-128">重命名</span><span class="sxs-lookup"><span data-stu-id="312c8-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="312c8-129">捆绑和缩小的影响</span><span class="sxs-lookup"><span data-stu-id="312c8-129">Impact of bundling and minification</span></span>

<span data-ttu-id="312c8-130">下表概述了单独加载资产与使用捆绑和缩小之间的差异：</span><span class="sxs-lookup"><span data-stu-id="312c8-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="312c8-131">操作</span><span class="sxs-lookup"><span data-stu-id="312c8-131">Action</span></span> | <span data-ttu-id="312c8-132">使用捆绑/缩小</span><span class="sxs-lookup"><span data-stu-id="312c8-132">With B/M</span></span> | <span data-ttu-id="312c8-133">不使用捆绑/缩小</span><span class="sxs-lookup"><span data-stu-id="312c8-133">Without B/M</span></span> | <span data-ttu-id="312c8-134">更改</span><span class="sxs-lookup"><span data-stu-id="312c8-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="312c8-135">文件请求</span><span class="sxs-lookup"><span data-stu-id="312c8-135">File Requests</span></span>  | <span data-ttu-id="312c8-136">7</span><span class="sxs-lookup"><span data-stu-id="312c8-136">7</span></span>   | <span data-ttu-id="312c8-137">18</span><span class="sxs-lookup"><span data-stu-id="312c8-137">18</span></span>     | <span data-ttu-id="312c8-138">157%</span><span class="sxs-lookup"><span data-stu-id="312c8-138">157%</span></span>
<span data-ttu-id="312c8-139">传输的 KB</span><span class="sxs-lookup"><span data-stu-id="312c8-139">KB Transferred</span></span> | <span data-ttu-id="312c8-140">156</span><span class="sxs-lookup"><span data-stu-id="312c8-140">156</span></span> | <span data-ttu-id="312c8-141">264.68</span><span class="sxs-lookup"><span data-stu-id="312c8-141">264.68</span></span> | <span data-ttu-id="312c8-142">70%</span><span class="sxs-lookup"><span data-stu-id="312c8-142">70%</span></span>
<span data-ttu-id="312c8-143">加载时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="312c8-143">Load Time (ms)</span></span> | <span data-ttu-id="312c8-144">885</span><span class="sxs-lookup"><span data-stu-id="312c8-144">885</span></span> | <span data-ttu-id="312c8-145">2360</span><span class="sxs-lookup"><span data-stu-id="312c8-145">2360</span></span>   | <span data-ttu-id="312c8-146">167%</span><span class="sxs-lookup"><span data-stu-id="312c8-146">167%</span></span>

<span data-ttu-id="312c8-147">对于 HTTP 请求标头，浏览器非常详细。</span><span class="sxs-lookup"><span data-stu-id="312c8-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="312c8-148">捆绑时，已发送的总字节数指标明显减少。</span><span class="sxs-lookup"><span data-stu-id="312c8-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="312c8-149">加载时间显示了显著改进，但本示例在本地运行。</span><span class="sxs-lookup"><span data-stu-id="312c8-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="312c8-150">将捆绑和缩小与通过网络传输的资产结合使用时，可实现更高的性能提升。</span><span class="sxs-lookup"><span data-stu-id="312c8-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="312c8-151">选择捆绑和缩小策略</span><span class="sxs-lookup"><span data-stu-id="312c8-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="312c8-152">MVC 和 Razor Pages 项目模板提供了一种用于捆绑和缩小的解决方案，它们构成 JSON 配置文件。</span><span class="sxs-lookup"><span data-stu-id="312c8-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="312c8-153">第三方工具（如 [Grunt](xref:client-side/using-grunt) 任务运行程序）以更复杂的方式完成相同的任务。</span><span class="sxs-lookup"><span data-stu-id="312c8-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="312c8-154">开发工作流需要捆绑和缩小之外的其他处理（如 linting 和图像优化）时，第三方工具非常适用。</span><span class="sxs-lookup"><span data-stu-id="312c8-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="312c8-155">通过使用设计时捆绑和缩小，在应用部署之前创建缩小文件。</span><span class="sxs-lookup"><span data-stu-id="312c8-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="312c8-156">在部署之前进行捆绑和缩小具有减少服务器负载的优点。</span><span class="sxs-lookup"><span data-stu-id="312c8-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="312c8-157">但是，必须认识到，设计时捆绑和缩小会增加生成的复杂性，并且仅适用于静态文件。</span><span class="sxs-lookup"><span data-stu-id="312c8-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="312c8-158">配置捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="312c8-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="312c8-159">在 ASP.NET Core 2.0 或更早版本中，MVC 和 Razor Pages 项目模板提供了一个 bundleconfig.json 配置文件，该文件定义每个捆绑的选项：</span><span class="sxs-lookup"><span data-stu-id="312c8-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="312c8-160">在 ASP.NET Core 2.1 或更高版本中，将名为 bundleconfig.json 的新 JSON 文件添加到 MVC 或 Razor Pages 项目根目录。</span><span class="sxs-lookup"><span data-stu-id="312c8-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="312c8-161">在该文件中包含以下 JSON 作为起点：</span><span class="sxs-lookup"><span data-stu-id="312c8-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="312c8-162">bundleconfig.json 文件定义每个捆绑的选项。</span><span class="sxs-lookup"><span data-stu-id="312c8-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="312c8-163">在前面的示例中，为自定义 JavaScript (wwwroot/js/site.js) 和样式表 (wwwroot/css/site.css) 文件定义了单一捆绑配置 。</span><span class="sxs-lookup"><span data-stu-id="312c8-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="312c8-164">配置选项包括：</span><span class="sxs-lookup"><span data-stu-id="312c8-164">Configuration options include:</span></span>

* <span data-ttu-id="312c8-165">`outputFileName`：要输出的捆绑文件的名称。</span><span class="sxs-lookup"><span data-stu-id="312c8-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="312c8-166">可包含 bundleconfig.json 文件中的相对路径。</span><span class="sxs-lookup"><span data-stu-id="312c8-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="312c8-167">（必需）</span><span class="sxs-lookup"><span data-stu-id="312c8-167">**required**</span></span>
* <span data-ttu-id="312c8-168">`inputFiles`：要捆绑在一起的文件数组。</span><span class="sxs-lookup"><span data-stu-id="312c8-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="312c8-169">这些是配置文件的相对路径。</span><span class="sxs-lookup"><span data-stu-id="312c8-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="312c8-170">可以选择使用空值，\*这将导致输出文件为空。</span><span class="sxs-lookup"><span data-stu-id="312c8-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="312c8-171">支持 [glob](https://www.tldp.org/LDP/abs/html/globbingref.html) 模式。</span><span class="sxs-lookup"><span data-stu-id="312c8-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="312c8-172">`minify`：输出类型的缩小选项。</span><span class="sxs-lookup"><span data-stu-id="312c8-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="312c8-173">可选，默认值 - `minify: { enabled: true }`</span><span class="sxs-lookup"><span data-stu-id="312c8-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="312c8-174">每个输出文件类型都有配置选项。</span><span class="sxs-lookup"><span data-stu-id="312c8-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="312c8-175">CSS 缩小程序</span><span class="sxs-lookup"><span data-stu-id="312c8-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="312c8-176">JavaScript 缩减程序</span><span class="sxs-lookup"><span data-stu-id="312c8-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="312c8-177">HTML 缩小程序</span><span class="sxs-lookup"><span data-stu-id="312c8-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="312c8-178">`includeInProject`：指示是否将生成的文件添加到项目文件的标记。</span><span class="sxs-lookup"><span data-stu-id="312c8-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="312c8-179">可选，默认值 - false</span><span class="sxs-lookup"><span data-stu-id="312c8-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="312c8-180">`sourceMap`：指示是否为捆绑的文件生成源映射的标记。</span><span class="sxs-lookup"><span data-stu-id="312c8-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="312c8-181">可选，默认值 - false</span><span class="sxs-lookup"><span data-stu-id="312c8-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="312c8-182">`sourceMapRootPath`：用于存储所生成的源映射文件的根路径。</span><span class="sxs-lookup"><span data-stu-id="312c8-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="312c8-183">向工作流添加文件</span><span class="sxs-lookup"><span data-stu-id="312c8-183">Add files to workflow</span></span>

<span data-ttu-id="312c8-184">假设添加了额外的 custom.css 文件，类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="312c8-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="312c8-185">若要缩小 custom.css 并将其与 site.css 捆绑到 site.min.css 文件中，请将相对路径添加到 bundleconfig.json   ：</span><span class="sxs-lookup"><span data-stu-id="312c8-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="312c8-186">或者，可以使用以下通配模式：</span><span class="sxs-lookup"><span data-stu-id="312c8-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="312c8-187">此通配模式匹配所有 CSS 文件，并排除缩小的文件模式。</span><span class="sxs-lookup"><span data-stu-id="312c8-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="312c8-188">生成应用程序。</span><span class="sxs-lookup"><span data-stu-id="312c8-188">Build the application.</span></span> <span data-ttu-id="312c8-189">打开 site.min.css 并注意 custom.css 的内容将追加到文件末尾 。</span><span class="sxs-lookup"><span data-stu-id="312c8-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="312c8-190">基于环境的捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="312c8-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="312c8-191">最佳做法是，应在生产环境中使用应用的捆绑文件和缩小文件。</span><span class="sxs-lookup"><span data-stu-id="312c8-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="312c8-192">在开发过程中，原始文件可简化应用的调试。</span><span class="sxs-lookup"><span data-stu-id="312c8-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="312c8-193">使用视图中的[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)指定要包含在页面中的文件。</span><span class="sxs-lookup"><span data-stu-id="312c8-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="312c8-194">环境标记帮助程序仅在特定[环境](xref:fundamentals/environments)中运行时呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="312c8-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="312c8-195">以下 `environment` 标记将在 `Development` 环境中运行时呈现未处理的 CSS 文件：</span><span class="sxs-lookup"><span data-stu-id="312c8-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="312c8-196">以下 `environment` 标记将在非 `Development` 环境中运行时呈现捆绑的和缩小的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="312c8-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="312c8-197">例如，在 `Production` 或 `Staging` 中运行将触发这些样式表的呈现：</span><span class="sxs-lookup"><span data-stu-id="312c8-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="312c8-198">从 Gulp 使用 bundleconfig.json</span><span class="sxs-lookup"><span data-stu-id="312c8-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="312c8-199">在某些情况下，应用的捆绑和缩小工作流需要额外处理。</span><span class="sxs-lookup"><span data-stu-id="312c8-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="312c8-200">示例包括图像优化、缓存清除和 CDN 资产处理。</span><span class="sxs-lookup"><span data-stu-id="312c8-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="312c8-201">为了满足这些要求，可以将捆绑和缩小工作流转换为使用 Gulp。</span><span class="sxs-lookup"><span data-stu-id="312c8-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="312c8-202">手动转换捆绑和缩小工作流以使用 Gulp</span><span class="sxs-lookup"><span data-stu-id="312c8-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="312c8-203">将 package.json 文件（包含以下 `devDependencies`）添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="312c8-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="312c8-204">`gulp-uglify` 模块不支持 ECMAScript (ES) 2015/ES6 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="312c8-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="312c8-205">安装 [gulp-terser](https://www.npmjs.com/package/gulp-terser) 而不是 `gulp-uglify` 来使用 ES2015/ES6 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="312c8-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="312c8-206">通过在与 package.json 相同的级别运行以下命令来安装依赖项：</span><span class="sxs-lookup"><span data-stu-id="312c8-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="312c8-207">安装 Gulp CLI 作为全局依赖项：</span><span class="sxs-lookup"><span data-stu-id="312c8-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="312c8-208">将以下 gulpfile.js 文件复制到项目根：</span><span class="sxs-lookup"><span data-stu-id="312c8-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="312c8-209">运行 Gulp 任务</span><span class="sxs-lookup"><span data-stu-id="312c8-209">Run Gulp tasks</span></span>

<span data-ttu-id="312c8-210">若要在 Visual Studio 中生成项目之前触发 Gulp 缩小任务，请将以下 [MSBuild 目标](/visualstudio/msbuild/msbuild-targets)添加到 \*.csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="312c8-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="312c8-211">在此示例中，`MyPreCompileTarget` 目标内定义的所有任务在预定义的 `Build` 目标之前运行。</span><span class="sxs-lookup"><span data-stu-id="312c8-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="312c8-212">Visual Studio 的输出窗口中显示类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="312c8-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="312c8-213">其他资源</span><span class="sxs-lookup"><span data-stu-id="312c8-213">Additional resources</span></span>

* [<span data-ttu-id="312c8-214">使用 Grunt</span><span class="sxs-lookup"><span data-stu-id="312c8-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="312c8-215">使用多个环境</span><span class="sxs-lookup"><span data-stu-id="312c8-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="312c8-216">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="312c8-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
