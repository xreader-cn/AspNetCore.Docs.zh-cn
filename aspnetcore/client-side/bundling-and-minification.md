---
title: ASP.NET Core 中的捆绑和缩小静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩减技术优化 ASP.NET Core web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7499381a24a2513a4fbd1205f245e624c86647c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080561"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core 中的捆绑和缩小静态资产

作者： [Scott Addie](https://twitter.com/Scott_Addie)和[David 松树](https://twitter.com/davidpine7)

本文介绍了应用绑定和缩减，包括如何使用 ASP.NET Core web apps 使用这些功能的好处。

## <a name="what-is-bundling-and-minification"></a>什么是绑定和缩减

绑定和缩减是可以在 web 应用中应用的两个不同的性能优化。 绑定和缩减一起使用，可减少服务器请求数并减小请求的静态资产的大小，从而提高性能。

绑定和缩减主要改善第一页请求加载时间。 请求网页后，浏览器会缓存静态资产（JavaScript、CSS 和图像）。 因此，当在同一站点上请求相同的资源时，绑定和缩减不会提高性能。 如果未在资产上正确设置 expires 标头，且未使用捆绑和缩减，则浏览器的新鲜度试探法会在几天后将资产过期。 此外，浏览器还需要对每个资产进行验证请求。 在这种情况下，绑定和缩减在第一次请求页面后仍能改善性能。

### <a name="bundling"></a>捆绑

绑定将多个文件合并到一个文件中。 绑定可减少呈现 web 资产（如网页）所需的服务器请求数。 可以专门为 CSS、JavaScript 等创建任意数量的单独包。文件越少，从浏览器到服务器的 HTTP 请求或提供应用程序的服务就会减少。 这会提高第一页的加载性能。

### <a name="minification"></a>缩减

缩减从代码中删除不必要的字符，而不更改功能。 因此，请求的资产（如 CSS、图像和 JavaScript 文件）的大小大幅减小。 缩减的常见副作用包括将变量名称缩短为一个字符、删除注释和不必要的空格。

请考虑以下 JavaScript 函数：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

缩减将函数降到了以下内容：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

除了删除注释和不必要的空格外，还会将以下参数和变量名称重命名为：

原始 | 重命名
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>捆绑和缩减的影响

下表概述了单独加载资产与使用绑定和缩减之间的差异：

操作 | 带有 B/M 的 | 无 B/M | 更改
--- | :---: | :---: | :---:
文件请求  | 7   | 18     | 157%
已传输 KB | 156 | 264.68 | 70%
加载时间（毫秒） | 885 | 2360   | 167%

对于 HTTP 请求标头，浏览器非常详细。 绑定的字节总数指标明显减少了绑定的时间。 加载时间显示了显著改进，但本示例在本地运行。 将捆绑与缩减与通过网络传输的资产结合使用时，可实现更高的性能提升。

## <a name="choose-a-bundling-and-minification-strategy"></a>选择捆绑和缩减策略

MVC 和 Razor Pages 项目模板提供了一种现成的解决方案，可用于缩减和 JSON 配置文件。 第三方工具（如[Grunt](xref:client-side/using-grunt)任务运行程序）以更复杂的方式完成相同的任务。 当开发工作流需要处理超过绑定和缩减&mdash;（例如 linting 和图像优化）时，第三方工具非常合适。 通过使用设计时绑定和缩减，缩小文件是在应用部署之前创建的。 在部署之前绑定和缩小提供了降低服务器负载的优点。 但是，必须认识到，设计时绑定和缩减会增加生成的复杂性，并且仅适用于静态文件。

## <a name="configure-bundling-and-minification"></a>配置捆绑和缩减

::: moniker range="<= aspnetcore-2.0"

在 ASP.NET Core 2.0 或更早版本中，MVC 和 Razor Pages 项目模板提供了一个*bundleconfig*配置文件，该文件定义每个绑定的选项：

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更高版本中，将名为*bundleconfig*的新 JSON 文件添加到 MVC 或 Razor Pages 项目根。 在该文件中包含以下 JSON 作为起始点：

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*Bundleconfig*文件定义每个绑定的选项。 在前面的示例中，为自定义 JavaScript （*wwwroot/js/node.js*）和样式表（*wwwroot/css/站点导航*）文件定义了单个捆绑配置。

配置选项包括:

* `outputFileName`：要输出的绑定文件的名称。 可以包含*bundleconfig*文件中的相对路径。 **必填**
* `inputFiles`：要捆绑在一起的文件的数组。 这些是配置文件的相对路径。 **可选**，* 空值将导致空的输出文件。 支持[通配](https://www.tldp.org/LDP/abs/html/globbingref.html)模式。
* `minify`：输出类型的缩减选项。 **可选**，*默认值`minify: { enabled: true }` -*
  * 每个输出文件类型都有配置选项。
    * [CSS 缩小器](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript 缩小器](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML 缩小器](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`：指示是否将生成的文件添加到项目文件的标记。 **可选**，*默认值为 false*
* `sourceMap`：指示是否为捆绑的文件生成源映射的标记。 **可选**，*默认值为 false*
* `sourceMapRootPath`：用于存储所生成的源映射文件的根路径。

## <a name="build-time-execution-of-bundling-and-minification"></a>绑定和缩减的生成时执行

[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet 包允许在生成时执行绑定和缩减。 包注入在生成和清理时间运行的[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)。 *Bundleconfig*文件由生成过程进行分析，以便基于定义的配置生成输出文件。

> [!NOTE]
> BuildBundlerMinifier 属于 GitHub 上的社区驱动项目，Microsoft 不提供支持。 应在[此处](https://github.com/madskristensen/BundlerMinifier/issues)归档问题。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

将*BuildBundlerMinifier*包添加到项目。

生成项目。 "输出" 窗口中将显示以下内容：

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

清理项目。 "输出" 窗口中将显示以下内容：

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

将*BuildBundlerMinifier*包添加到项目：

```dotnetcli
dotnet add package BuildBundlerMinifier
```

如果使用 ASP.NET Core 1.x，还原新添加的包：

```dotnetcli
dotnet restore
```

生成项目：

```dotnetcli
dotnet build
```

将显示以下内容：

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

清理项目：

```dotnetcli
dotnet clean
```

将显示以下输出：

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>绑定和缩减的即席执行

可以在不生成项目的情况下即席运行捆绑和缩减任务。 将[BundlerMinifier](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet 包添加到项目：

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier 属于 GitHub 上的社区驱动项目，Microsoft 不提供支持。 应在[此处](https://github.com/madskristensen/BundlerMinifier/issues)归档问题。

此包扩展以包括.NET Core CLI *dotnet 捆绑*工具。 可以在 "包管理器控制台" （PMC）窗口或命令行界面中执行以下命令：

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> NuGet 包管理器将依赖项添加到 * .csproj 文件`<PackageReference />`作为节点。 `dotnet bundle`命令注册.NET Core CLI 时，才`<DotNetCliToolReference />`使用节点。 请相应地修改 * .csproj 文件。

## <a name="add-files-to-workflow"></a>向工作流添加文件

假设添加了其他*自定义 .css*文件的示例类似于以下内容：

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

若要缩小*自定义 css*并将其与*站点* *中的内容*捆绑在一起，请将相对路径添加到*bundleconfig*：

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> 或者，可以使用以下组合模式：
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> 此组合模式匹配所有 CSS 文件，并排除缩小文件模式。

生成应用程序。 打开 "*网站"。最小 css* ，请注意，*自定义 css*的内容将追加到文件末尾。

## <a name="environment-based-bundling-and-minification"></a>基于环境的捆绑和缩减

最佳做法是，应在生产环境中使用应用的捆绑文件和缩小文件。 在开发过程中，原始文件可简化应用程序的调试。

通过在视图中使用[环境标记帮助器](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)来指定要包含在页面中的文件。 环境标记帮助程序仅在特定[环境](xref:fundamentals/environments)中运行时呈现其内容。

在环境中运行时，以下`environment`标记将呈现未处理的 CSS 文件： `Development`

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

当在`environment`以外的环境`Development`中运行时，以下标记将呈现捆绑的和缩小的 CSS 文件。 例如，在中`Production`运行或`Staging`触发这些样式表的呈现：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>从 Gulp 使用 bundleconfig

在某些情况下，应用的绑定和缩减工作流需要额外处理。 示例包括图像优化、缓存清除和 CDN 资产处理。 为了满足这些要求，你可以将绑定和缩减工作流转换为使用 Gulp。

### <a name="use-the-bundler--minifier-extension"></a>使用捆绑程序 & 缩小器扩展

Visual Studio[捆绑程序 & 缩小器](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)扩展处理到 Gulp 的转换。

> [!NOTE]
> 捆绑程序 & 缩小器扩展属于 Microsoft 不提供支持的 GitHub 上的社区驱动项目。 应在[此处](https://github.com/madskristensen/BundlerMinifier/issues)归档问题。

右键单击解决方案资源管理器中的*bundleconfig*文件，然后选择**捆绑程序 & 缩小器** > **转换为 Gulp ...** ：

![转换为 Gulp 上下文菜单项](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

将*gulpfile*和*包*文件添加到项目。 安装了*package. json*文件的`devDependencies`部分中列出的支持[npm](https://www.npmjs.com/)包。

在 PMC 窗口中运行以下命令，以将 Gulp CLI 作为全局依赖项安装：

```console
npm i -g gulp-cli
```

*Gulpfile*文件读取输入、输出和设置的*bundleconfig*文件。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>手动转换

如果 Visual Studio 和/或捆绑程序 & 缩小器扩展不可用，请手动转换。

将*包 json*文件`devDependencies`添加到项目根目录：

> [!WARNING]
> 该`gulp-uglify`模块不支持 ECMAScript （ES） 2015/ES6 和更高版本。 安装[gulp-terser](https://www.npmjs.com/package/gulp-terser) `gulp-uglify` ，而不是使用 ES2015/ES6 或更高版本。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

通过在与 package 相同的级别上运行以下命令来安装依赖项 *。 json*：

```console
npm i
```

安装 Gulp CLI 作为全局依赖项：

```console
npm i -g gulp-cli
```

将下面的*gulpfile*文件复制到项目根：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>运行 Gulp 任务

若要在 Visual Studio 中生成项目之前触发 Gulp 缩减任务，请将以下[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)添加到 * .csproj 文件：

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

在此示例中，在`MyPreCompileTarget`目标内定义的任何任务都在预定义`Build`目标之前运行。 Visual Studio 的输出窗口中显示类似于以下内容的输出：

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


## <a name="additional-resources"></a>其他资源

* [使用 Grunt](xref:client-side/using-grunt)
* [使用多个环境](xref:fundamentals/environments)
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)
