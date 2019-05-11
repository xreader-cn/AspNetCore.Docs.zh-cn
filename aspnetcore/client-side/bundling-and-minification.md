---
title: 捆绑和缩小在 ASP.NET Core 中的静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩减技术优化 ASP.NET Core web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 05/10/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: ba01d365a25dfbd13fed89263d7489b2ce2a8771
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65535931"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>捆绑和缩小在 ASP.NET Core 中的静态资产

通过[Scott Addie](https://twitter.com/Scott_Addie)和[David 松树](https://twitter.com/davidpine7)

本文介绍了应用绑定和缩减，包括如何使用 ASP.NET Core web apps 使用这些功能的好处。

## <a name="what-is-bundling-and-minification"></a>捆绑和缩小是什么

捆绑和缩小可以应用的 web 应用中的两个明显的性能优化。 一起使用时，捆绑和缩小通过提高性能降低的服务器请求数和减小请求的静态资产的大小。

捆绑和缩小主要提高第一个页面请求加载时间。 一旦请求 web 页，在浏览器缓存静态资产 （JavaScript、 CSS 和图像）。 因此，绑定和缩减不提高性能时请求同一页上或请求同一资产相同站点上的页。 如果过期标头未正确设置对资产并没有使用绑定和缩减，如果浏览器的新鲜度试探法将标记资产过时几天后。 此外，在浏览器为每个资产需要验证请求。 在这种情况下，绑定和缩减提供性能改进甚至后第一个页面请求。

### <a name="bundling"></a>捆绑

捆绑将多个文件合并到单个文件。 绑定可减少所需呈现 web 资产，例如网页的服务器请求的数量。 专门为 CSS、 JavaScript 等，可以创建任意数量的单独的捆绑包。更少的文件意味着较少的 HTTP 请求从浏览器到服务器或提供你的应用程序的服务。 这会改进了第一个页面加载性能。

### <a name="minification"></a>缩小

缩减而无需更改功能从代码中删除不必要的字符。 结果是很大大小减少请求的资产 （如 CSS、 图像和 JavaScript 文件）。 缩减的常见副作用包括缩短为一个字符的变量名称和删除注释和不必要的空格。

请考虑以下的 JavaScript 函数：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

缩小简化为以下函数：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

除了删除注释和不必要的空格，以下参数和变量名称已重命名，如下所示：

原始 | 重命名
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>捆绑和缩减的影响

下表概述了单独加载资产与使用绑定和缩减之间的差异：

操作 | B/m | Without B/M | 更改
--- | :---: | :---: | :---:
文件请求  | 7   | 18     | 157%
传输的 KB | 156 | 264.68 | 70%
加载时间 （毫秒） | 885 | 2360   | 167%

浏览器是相当冗长关于 HTTP 请求标头。 总字节数发送指标时将看到显著降低。 加载时间显示了重大改进，但此示例在本地运行。 与资产使用捆绑和缩小通过网络传输时实现更高的性能提升。

## <a name="choose-a-bundling-and-minification-strategy"></a>选择捆绑和缩减策略

MVC 和 Razor 页面项目模板提供开箱解决方案捆绑和缩小包含 JSON 配置文件。 第三方工具，如[Grunt](xref:client-side/using-grunt)任务运行程序中，完成相同的任务具有更多的复杂性。 第三方工具是理想的选择时开发工作流需要处理超出捆绑和缩小&mdash;linting 和映像优化等。 通过使用设计时绑定和缩减，在应用程序的部署前创建缩小化的文件。 捆绑和缩小在部署前提供减少的服务器负载的优点。 但是，务必要识别该设计时捆绑和缩小会增大复杂性，生成和仅适用于静态文件。

## <a name="configure-bundling-and-minification"></a>配置捆绑和缩减

::: moniker range="<= aspnetcore-2.0"

MVC 和 Razor 页面项目模板提供了在 ASP.NET Core 2.0 或更早*bundleconfig.json*定义每个捆绑包的选项的配置文件：

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更高版本，添加一个新的 JSON 文件，名为*bundleconfig.json*，与 MVC 或 Razor 页面项目根目录。 作为起始点，在该文件中包含以下 JSON:

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

*Bundleconfig.json*文件定义每个捆绑包的选项。 在上述示例中，一个捆绑包配置为定义了自定义 JavaScript (*wwwroot/js/site.js*) 和样式表 (*wwwroot/css/site.css*) 文件。

配置选项包括：

* `outputFileName`：要输出的捆绑包文件的名称。 可以包含中的相对路径*bundleconfig.json*文件。 **required**
* `inputFiles`：要捆绑在一起的文件的数组。 这些是配置文件的相对路径。 **可选**，* 空值会导致空的输出文件。 [通配](http://www.tldp.org/LDP/abs/html/globbingref.html)支持模式。
* `minify`：输出类型缩小选项。 **可选**，*默认值- `minify: { enabled: true }`*
  * 每个输出文件类型提供了配置选项。
    * [CSS 缩小器](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript 缩减程序](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML 缩小器](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`：指示是否将生成的文件添加到项目文件的标记。 **可选**，*默认值-false*
* `sourceMap`：指示是否生成将捆绑的文件的源映射的标记。 **可选**，*默认值-false*
* `sourceMapRootPath`：用于存储生成的源映射文件的根路径。

## <a name="build-time-execution-of-bundling-and-minification"></a>生成时执行的捆绑和缩小

[BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet 程序包可执行的捆绑和缩小在生成时启用。 包将注入[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)其在生成和清理的时间运行。 *Bundleconfig.json*文件分析的生成过程以生成基于定义的配置的输出文件。

> [!NOTE]
> BuildBundlerMinifier 属于 Microsoft 为其提供任何支持的 GitHub 上的社区驱动项目。 问题应将归档[此处](https://github.com/madskristensen/BundlerMinifier/issues)。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

添加*BuildBundlerMinifier*包到你的项目。

生成项目。 将显示以下输出窗口中：

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

清除该项目。 将显示以下输出窗口中：

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

添加*BuildBundlerMinifier*包到你的项目：

```console
dotnet add package BuildBundlerMinifier
```

如果使用 ASP.NET Core 1.x，还原新添加的包：

```console
dotnet restore
```

生成项目：

```console
dotnet build
```

将显示以下：

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

清除该项目：

```console
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

## <a name="ad-hoc-execution-of-bundling-and-minification"></a>捆绑和缩小的即席执行

很可能在某些特殊情况，运行绑定和缩减任务，而无需生成项目。 添加[BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/)到你的项目的 NuGet 包：

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> BundlerMinifier.Core 属于 Microsoft 为其提供任何支持的 GitHub 上的社区驱动项目。 问题应将归档[此处](https://github.com/madskristensen/BundlerMinifier/issues)。

此包扩展以包括.NET Core CLI *dotnet 捆绑*工具。 在包管理器控制台 (PMC) 窗口或命令行界面中，可以执行以下命令：

```console
dotnet bundle
```

> [!IMPORTANT]
> NuGet 包管理器将依赖项添加到 *.csproj 文件作为`<PackageReference />`节点。 `dotnet bundle`命令注册.NET Core CLI 时，才`<DotNetCliToolReference />`使用节点。 相应地修改 *.csproj 文件。

## <a name="add-files-to-workflow"></a>将文件添加到工作流

在其中一个示例，请考虑额外*custom.css*与下面类似的添加文件：

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

若要缩小*custom.css*并将其与捆绑*site.css*到*site.min.css*文件中，添加的相对路径*bundleconfig.json*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> 或者，可使用以下通配模式：
>
> ```json
> "inputFiles": ["wwwroot/**/*(*.css|!(*.min.css))"]
> ```
>
> 此通配模式匹配所有 CSS 文件，并不包括缩小的文件模式。

生成应用程序。 打开*site.min.css* ，并注意的内容*custom.css*追加到文件末尾。

## <a name="environment-based-bundling-and-minification"></a>基于环境的捆绑和缩减

作为最佳做法，应在生产环境中使用你的应用的捆绑和缩小化文件。 在开发期间，为方便调试的应用程序将使原始文件。

指定要使用包括在页面中的文件[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)在视图中。 环境标记帮助程序中特定于运行时只呈现其内容[环境](xref:fundamentals/environments)。

以下`environment`标记将转换的未处理的 CSS 文件在运行时`Development`环境：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

以下`environment`标记呈现的捆绑和缩小 CSS 文件，而不在环境中运行时`Development`。 例如，在运行`Production`或`Staging`触发这些样式表的呈现：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>使用 Gulp 从 bundleconfig.json

有些情况下在其中应用的捆绑和缩小工作流需要进行其他处理。 示例包括图像优化、 清除缓存，和 CDN 资产处理。 若要满足这些要求，可以将转换使用 Gulp 的捆绑和缩小工作流。

### <a name="use-the-bundler--minifier-extension"></a>使用捆绑程序和缩小器扩展

Visual Studio[捆绑程序和缩小器](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier)扩展处理到 Gulp 的转换。

> [!NOTE]
> 捆绑程序和缩小器扩展属于 Microsoft 为其提供任何支持的 GitHub 上的社区驱动项目。 问题应将归档[此处](https://github.com/madskristensen/BundlerMinifier/issues)。

右键单击*bundleconfig.json*文件在解决方案资源管理器中，然后选择**捆绑程序和缩小器** > **转换 Gulp...**:

![将转换到 Gulp 上下文菜单项](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

*Gulpfile.js*并*package.json*文件添加到项目。 支持[npm](https://www.npmjs.com/)中列出的包*package.json*文件的`devDependencies`部分安装。

若要安装 Gulp CLI 作为全局依赖项在 PMC 窗口中运行以下命令：

```console
npm i -g gulp-cli
```

*Gulpfile.js*文件读取*bundleconfig.json*输入、 输出和设置的文件。

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a>手动转换

如果 Visual Studio 和/或捆绑程序和缩小器扩展插件不可用，手动转换。

添加*package.json*文件中的，使用以下`devDependencies`，到项目根目录：

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

通过与同一级别中运行以下命令安装依赖项*package.json*:

```console
npm i
```

为全局的依赖项安装 Gulp CLI:

```console
npm i -g gulp-cli
```

复制*gulpfile.js*到项目根文件如下：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>运行 Gulp 任务

若要触发 Gulp 缩小任务之前在 Visual Studio 中生成项目，添加以下[MSBuild 目标](/visualstudio/msbuild/msbuild-targets)到 *.csproj 文件：

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

在此示例中定义的任何任务`MyPreCompileTarget`目标运行之前在预定义`Build`目标。 在 Visual Studio 输出窗口中显示类似于以下输出：

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
