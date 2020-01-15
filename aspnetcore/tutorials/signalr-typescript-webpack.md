---
title: 配合使用 ASP.NET Core SignalR 和 TypeScript 以及 Webpack
author: ssougnez
description: 在本教程中，我们将配置 Webpack，以捆绑和生成 ASP.NET Core SignalR Web 应用，该应用的客户端是使用 TypeScript 编写的。
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 9094a1d391c087a6f58aa9dd66e3697a79f4af86
ms.sourcegitcommit: ef1720cb733908f36a54825d84c3461c5280bdbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737511"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="6a4b1-103">配合使用 ASP.NET Core SignalR 和 TypeScript 以及 Webpack</span><span class="sxs-lookup"><span data-stu-id="6a4b1-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="6a4b1-104">作者：[Sébastien Sougnez](https://twitter.com/ssougnez) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6a4b1-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6a4b1-105">开发人员可以通过 [Webpack](https://webpack.js.org/) 捆绑和生成 Web 应用的客户端资源。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="6a4b1-106">本教程介绍在 ASP.NET Core SignalR Web 应用中使用 Webpack，该应用的客户端是使用 [TypeScript](https://www.typescriptlang.org/) 编写的。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="6a4b1-107">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6a4b1-108">为入门 ASP.NET Core SignalR 应用搭建基架</span><span class="sxs-lookup"><span data-stu-id="6a4b1-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="6a4b1-109">配置 SignalR TypeScript 客户端</span><span class="sxs-lookup"><span data-stu-id="6a4b1-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="6a4b1-110">使用 Webpack 配置生成管道</span><span class="sxs-lookup"><span data-stu-id="6a4b1-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="6a4b1-111">配置 SignalR 服务器</span><span class="sxs-lookup"><span data-stu-id="6a4b1-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="6a4b1-112">启用客户端和服务器之间的通信</span><span class="sxs-lookup"><span data-stu-id="6a4b1-112">Enable communication between client and server</span></span>

<span data-ttu-id="6a4b1-113">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6a4b1-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="6a4b1-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="6a4b1-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a4b1-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a4b1-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a4b1-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发  工作负载</span><span class="sxs-lookup"><span data-stu-id="6a4b1-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6a4b1-117">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6a4b1-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="6a4b1-118">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="6a4b1-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6a4b1-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6a4b1-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6a4b1-121">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6a4b1-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="6a4b1-122">适用于 Visual Studio Code 的 C# 版本 1.17.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6a4b1-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="6a4b1-123">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="6a4b1-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="6a4b1-124">创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="6a4b1-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a4b1-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a4b1-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a4b1-126">配置 Visual Studio，在 PATH 环境变量中查找 npm  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="6a4b1-127">默认情况下，Visual Studio 使用在安装目录中找到的 npm 版本。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="6a4b1-128">在 Visual Studio 中按照以下说明执行操作：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="6a4b1-129">导航到“工具”  >“选项”  >“项目和解决方案”  >“Web 包管理”  >“外部 Web 工具”  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="6a4b1-130">在列表中选择 $(PATH) 项  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="6a4b1-131">单击向上键将项移动列表第二个位置。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio 配置](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="6a4b1-133">已完成 Visual Studio 配置。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="6a4b1-134">可以开始创建项目了。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-134">It's time to create the project.</span></span>

1. <span data-ttu-id="6a4b1-135">使用“文件”>“新建”>“项目”菜单选项，然后选择“ASP.NET Core Web 应用程序”模板     。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="6a4b1-136">将项目命名为 SignalRWebPack 并选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="6a4b1-137">从目标框架下拉列表选择 .NET Core 并从框架选择器下拉列表选择 ASP.NET Core 3.0   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="6a4b1-138">选择“空白”模板并选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6a4b1-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a4b1-140">在“集成终端”中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="6a4b1-141">SignalRWebPack 目录中创建了一个面向 .NET Core 的空 ASP.NET Core Web 应用  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="6a4b1-142">配置 Webpack 和 TypeScript</span><span class="sxs-lookup"><span data-stu-id="6a4b1-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="6a4b1-143">以下步骤配置 TypeScript 到 JavaScript 的转换和客户端资源的捆绑。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="6a4b1-144">在项目根目录中执行以下命令，创建 package.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="6a4b1-145">将突出显示的属性添加到 package.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="6a4b1-146">将 `private` 属性设置为 `true`，防止下一步出现包安装警告。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="6a4b1-147">安装所需的 npm 包。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-147">Install the required npm packages.</span></span> <span data-ttu-id="6a4b1-148">从项目根执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="6a4b1-149">需要注意的一些命令细节：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-149">Some command details to note:</span></span>

    * <span data-ttu-id="6a4b1-150">每个包名称中 `@` 符号后是版本号。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="6a4b1-151">npm 安装这些特定的包版本。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="6a4b1-152">`-E` 选项禁用 npm 将[语义化版本控制](https://semver.org/)范围运算符写到 package.json 的默认行为  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="6a4b1-153">例如，使用 `"webpack": "4.29.3"` 而不是 `"webpack": "^4.29.3"`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="6a4b1-154">此选项防止意外升级到新的包版本。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="6a4b1-155">有关详细信息，请参阅官方 [npm-install](https://docs.npmjs.com/cli/install) 文档。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="6a4b1-156">将 package.json 文件的 `scripts` 属性替换为以下代码片段  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="6a4b1-157">脚本的一些解释：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="6a4b1-158">`build`：在开发模式下捆绑客户端资源并观察文件更改。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="6a4b1-159">文件观察程序使捆绑在每次项目文件发生更改时重新生成。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="6a4b1-160">`mode` 选项可禁用生产优化，例如摇树优化和缩小优化。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="6a4b1-161">仅在开发中使用 `build`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="6a4b1-162">`release`：在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="6a4b1-163">`publish`：运行 `release` 脚本，在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="6a4b1-164">它调用 .NET Core CLI 的 [publish](/dotnet/core/tools/dotnet-publish) 命令发布应用。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="6a4b1-165">在项目根中创建名为 webpack.config.js 的文件，包含以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="6a4b1-166">前面的文件配置 Webpack 编译。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="6a4b1-167">需要注意的一些配置细节：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="6a4b1-168">`output` 属性替代 dist 的默认值  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="6a4b1-169">捆绑反而在 wwwroot 目录中发出  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="6a4b1-170">`resolve.extensions` 数组包含 .js，以便导入 SignalR 客户端 JavaScript。 </span><span class="sxs-lookup"><span data-stu-id="6a4b1-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="6a4b1-171">在项目根中创建新的 src 目录  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="6a4b1-172">目的是存储项目的客户端资产。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="6a4b1-173">创建包含以下内容的 src/index.html  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="6a4b1-174">前面的 HTML 定义主页的样板标记。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="6a4b1-175">创建新的 src/css 目录  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="6a4b1-176">目的是存储项目的 .css 文件  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="6a4b1-177">创建包含以下内容的 src/css/main.css  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="6a4b1-178">前面的 main.css 文件设计应用样式  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="6a4b1-179">创建包含以下内容的 src/tsconfig.json  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="6a4b1-180">前面的代码配置 TypeScript 编译器，生成与 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 兼容的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="6a4b1-181">创建包含以下内容的 src/index.ts  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="6a4b1-182">前面的 TypeScript 检索对 DOM 元素的引用并附加两个事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="6a4b1-183">`keyup`：用户在文本框中键入标识为 `tbMessage` 的内容时触发此事件。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="6a4b1-184">用户按 Enter 时调用 `send` 函数  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="6a4b1-185">`click`：用户单击“发送”按钮时触发此事件  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="6a4b1-186">调用 `send` 函数。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="6a4b1-187">配置 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="6a4b1-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="6a4b1-188">在 `Startup.Configure` 方法中，添加对 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 和 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="6a4b1-189">前面的代码允许服务器定位并提供 index.html 文件，无论用户输入完整 URL 还是 Web 应用的根 URL  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="6a4b1-190">在 `Startup.Configure` 方法的末尾，将 /hub  路由映射到 `ChatHub` 中心。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="6a4b1-191">将显示 Hello World!  的代码</span><span class="sxs-lookup"><span data-stu-id="6a4b1-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="6a4b1-192">替换为以下行：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="6a4b1-193">在 `Startup.ConfigureServices` 方法中，调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="6a4b1-194">此操作会将 SignalR 服务添加到项目。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="6a4b1-195">在项目根中创建名为 Hubs 的新目录  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="6a4b1-196">目的是存储 SignalR 中心（在下一步中创建）。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="6a4b1-197">创建包含以下代码的中心 Hubs/ChatHub.cs  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="6a4b1-198">在 Startup.cs 文件顶部添加以下代码，解析 `ChatHub` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="6a4b1-199">启用客户端和服务器通信</span><span class="sxs-lookup"><span data-stu-id="6a4b1-199">Enable client and server communication</span></span>

<span data-ttu-id="6a4b1-200">应用当前显示一个发送消息的简单窗体。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="6a4b1-201">尝试执行此操作时没有任何反应。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="6a4b1-202">服务器正在侦听特定的路由，但是不涉及发送消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="6a4b1-203">在项目根执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="6a4b1-204">前面的命令将安装 [SignalR TypeScript 客户端](https://www.npmjs.com/package/@microsoft/signalr)，它允许客户端向服务器发送消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="6a4b1-205">将突出显示的代码添加到 src/index.ts 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="6a4b1-206">前面的代码支持从服务器接收消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="6a4b1-207">`HubConnectionBuilder` 类创建新的生成器，用于配置服务器连接。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="6a4b1-208">`withUrl` 函数配置中心 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-208">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="6a4b1-209"> 启用客户端和服务器之间的消息交换。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-209"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="6a4b1-210">每个消息都有特定的名称。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-210">Each message has a specific name.</span></span> <span data-ttu-id="6a4b1-211">例如，名为 `messageReceived` 的消息可以执行负责在消息区域显示新消息的逻辑。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="6a4b1-212">可以通过 `on` 函数完成对特定消息的侦听。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="6a4b1-213">可以侦听任意数量的消息名称。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-213">You can listen to any number of message names.</span></span> <span data-ttu-id="6a4b1-214">还可以将参数传递到消息，例如所接收消息的作者姓名和内容。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="6a4b1-215">客户端收到一条消息后，会创建一个新的 `div` 元素并在其 `innerHTML` 属性中显示作者姓名和消息内容。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="6a4b1-216">它添加到显示消息的主要 `div` 元素。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="6a4b1-217">客户端可以接收消息后，将它配置为发送消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="6a4b1-218">将突出显示的代码添加到 src/index.ts 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="6a4b1-219">通过 WebSockets 连接发送消息需要调用 `send` 方法。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="6a4b1-220">该方法的第一个参数是消息名称。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="6a4b1-221">消息数据包含其他参数。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="6a4b1-222">在此示例中，一条标识为 `newMessage` 的消息已发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="6a4b1-223">该消息包含用户名和文本框中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="6a4b1-224">如果发送成功，会清空文本框。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="6a4b1-225">将突出显示的方法添加到 `ChatHub` 类：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="6a4b1-226">服务器收到消息后，前面的代码会将这些消息播发到所有连接的用户。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="6a4b1-227">没有必要使用泛型 `on` 方法接收所有消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="6a4b1-228">使用以消息名称命名的方法就可以了。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="6a4b1-229">在此示例中，TypeScript 客户端发送一条标识为 `newMessage` 的消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="6a4b1-230">C# `NewMessage` 方法需要客户端发送的数据。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="6a4b1-231">在 [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) 调用 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 方法。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="6a4b1-232">接收的消息会发送到所有连接到中心的客户端。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="6a4b1-233">测试应用</span><span class="sxs-lookup"><span data-stu-id="6a4b1-233">Test the app</span></span>

<span data-ttu-id="6a4b1-234">确认应用遵循以下步骤。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a4b1-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a4b1-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6a4b1-236">在 release 模式下运行 Webpack  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="6a4b1-237">使用“包管理器控制台”窗口，在项目根中运行以下命令  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="6a4b1-238">如果不在项目根中，请在输入该命令之前输入 `cd SignalRWebPack`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="6a4b1-239">选择“调试” > “开始执行(不调试)”，在不附加调试器的情况下在浏览器中启动应用   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="6a4b1-240">在 `http://localhost:<port_number>` 上提供 *wwwroot/index.html* 文件。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="6a4b1-241">如果遇到编译错误，请尝试关闭并重新打开解决方案。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="6a4b1-242">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="6a4b1-243">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6a4b1-244">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="6a4b1-245">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6a4b1-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6a4b1-247">通过在项目根中执行以下命令以 release 模式运行 Webpack  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="6a4b1-248">通过在项目根中执行以下命令生成和运行应用：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="6a4b1-249">Web 服务器启动应用并在 localhost 上提供。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="6a4b1-250">打开浏览器，转到 `http://localhost:<port_number>`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="6a4b1-251">提供 wwwroot/index.html 文件  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="6a4b1-252">从地址栏复制 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="6a4b1-253">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="6a4b1-254">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6a4b1-255">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="6a4b1-256">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![两个浏览器窗口都显示的消息](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="6a4b1-258">先决条件</span><span class="sxs-lookup"><span data-stu-id="6a4b1-258">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a4b1-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a4b1-259">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a4b1-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发  工作负载</span><span class="sxs-lookup"><span data-stu-id="6a4b1-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="6a4b1-261">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6a4b1-261">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="6a4b1-262">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="6a4b1-262">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6a4b1-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-263">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="6a4b1-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-264">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="6a4b1-265">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6a4b1-265">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="6a4b1-266">适用于 Visual Studio Code 的 C# 版本 1.17.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="6a4b1-266">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="6a4b1-267">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="6a4b1-267">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="6a4b1-268">创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="6a4b1-268">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a4b1-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a4b1-269">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a4b1-270">配置 Visual Studio，在 PATH 环境变量中查找 npm  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-270">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="6a4b1-271">默认情况下，Visual Studio 使用在安装目录中找到的 npm 版本。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-271">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="6a4b1-272">在 Visual Studio 中按照以下说明执行操作：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-272">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="6a4b1-273">导航到“工具”  >“选项”  >“项目和解决方案”  >“Web 包管理”  >“外部 Web 工具”  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-273">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="6a4b1-274">在列表中选择 $(PATH) 项  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-274">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="6a4b1-275">单击向上键将项移动列表第二个位置。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-275">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio 配置](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="6a4b1-277">已完成 Visual Studio 配置。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-277">Visual Studio configuration is completed.</span></span> <span data-ttu-id="6a4b1-278">可以开始创建项目了。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-278">It's time to create the project.</span></span>

1. <span data-ttu-id="6a4b1-279">使用“文件”>“新建”>“项目”菜单选项，然后选择“ASP.NET Core Web 应用程序”模板     。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-279">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="6a4b1-280">将项目命名为 SignalRWebPack 并选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-280">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="6a4b1-281">从目标框架下拉列表选择 .NET Core 并从框架选择器下拉列表选择 ASP.NET Core 2.2   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-281">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="6a4b1-282">选择“空白”模板并选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-282">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6a4b1-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a4b1-284">在“集成终端”中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-284">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="6a4b1-285">SignalRWebPack 目录中创建了一个面向 .NET Core 的空 ASP.NET Core Web 应用  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-285">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="6a4b1-286">配置 Webpack 和 TypeScript</span><span class="sxs-lookup"><span data-stu-id="6a4b1-286">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="6a4b1-287">以下步骤配置 TypeScript 到 JavaScript 的转换和客户端资源的捆绑。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-287">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="6a4b1-288">在项目根目录中执行以下命令，创建 package.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-288">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="6a4b1-289">将突出显示的属性添加到 package.json 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-289">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="6a4b1-290">将 `private` 属性设置为 `true`，防止下一步出现包安装警告。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-290">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="6a4b1-291">安装所需的 npm 包。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-291">Install the required npm packages.</span></span> <span data-ttu-id="6a4b1-292">从项目根执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-292">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="6a4b1-293">需要注意的一些命令细节：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-293">Some command details to note:</span></span>

    * <span data-ttu-id="6a4b1-294">每个包名称中 `@` 符号后是版本号。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-294">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="6a4b1-295">npm 安装这些特定的包版本。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-295">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="6a4b1-296">`-E` 选项禁用 npm 将[语义化版本控制](https://semver.org/)范围运算符写到 package.json 的默认行为  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-296">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="6a4b1-297">例如，使用 `"webpack": "4.29.3"` 而不是 `"webpack": "^4.29.3"`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-297">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="6a4b1-298">此选项防止意外升级到新的包版本。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-298">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="6a4b1-299">有关详细信息，请参阅官方 [npm-install](https://docs.npmjs.com/cli/install) 文档。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-299">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="6a4b1-300">将 package.json 文件的 `scripts` 属性替换为以下代码片段  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-300">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="6a4b1-301">脚本的一些解释：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-301">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="6a4b1-302">`build`：在开发模式下捆绑客户端资源并观察文件更改。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-302">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="6a4b1-303">文件观察程序使捆绑在每次项目文件发生更改时重新生成。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-303">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="6a4b1-304">`mode` 选项可禁用生产优化，例如摇树优化和缩小优化。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-304">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="6a4b1-305">仅在开发中使用 `build`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-305">Only use `build` in development.</span></span>
    * <span data-ttu-id="6a4b1-306">`release`：在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-306">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="6a4b1-307">`publish`：运行 `release` 脚本，在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-307">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="6a4b1-308">它调用 .NET Core CLI 的 [publish](/dotnet/core/tools/dotnet-publish) 命令发布应用。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-308">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="6a4b1-309">在项目根中创建名为 webpack.config.js 的文件，包含以下内容  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-309">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="6a4b1-310">前面的文件配置 Webpack 编译。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-310">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="6a4b1-311">需要注意的一些配置细节：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-311">Some configuration details to note:</span></span>

    * <span data-ttu-id="6a4b1-312">`output` 属性替代 dist 的默认值  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-312">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="6a4b1-313">捆绑反而在 wwwroot 目录中发出  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-313">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="6a4b1-314">`resolve.extensions` 数组包含 .js，以便导入 SignalR 客户端 JavaScript。 </span><span class="sxs-lookup"><span data-stu-id="6a4b1-314">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="6a4b1-315">在项目根中创建新的 src 目录  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-315">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="6a4b1-316">目的是存储项目的客户端资产。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-316">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="6a4b1-317">创建包含以下内容的 src/index.html  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-317">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="6a4b1-318">前面的 HTML 定义主页的样板标记。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-318">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="6a4b1-319">创建新的 src/css 目录  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-319">Create a new *src/css* directory.</span></span> <span data-ttu-id="6a4b1-320">目的是存储项目的 .css 文件  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-320">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="6a4b1-321">创建包含以下内容的 src/css/main.css  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-321">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="6a4b1-322">前面的 main.css 文件设计应用样式  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-322">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="6a4b1-323">创建包含以下内容的 src/tsconfig.json  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-323">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="6a4b1-324">前面的代码配置 TypeScript 编译器，生成与 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 兼容的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-324">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="6a4b1-325">创建包含以下内容的 src/index.ts  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-325">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="6a4b1-326">前面的 TypeScript 检索对 DOM 元素的引用并附加两个事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-326">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="6a4b1-327">`keyup`：用户在文本框中键入标识为 `tbMessage` 的内容时触发此事件。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-327">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="6a4b1-328">用户按 Enter 时调用 `send` 函数  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-328">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="6a4b1-329">`click`：用户单击“发送”按钮时触发此事件  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-329">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="6a4b1-330">调用 `send` 函数。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-330">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="6a4b1-331">配置 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="6a4b1-331">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="6a4b1-332">`Startup.Configure` 方法中提供的代码显示 Hello World!  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-332">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="6a4b1-333">将 `app.Run` 方法调用替换为对 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 和 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-333">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="6a4b1-334">前面的代码允许服务器定位并提供 index.html 文件，无论用户输入完整 URL 还是 Web 应用的根 URL  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-334">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="6a4b1-335">在 `Startup.ConfigureServices` 方法中调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-335">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6a4b1-336">此操作会将 SignalR 服务添加到项目。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-336">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="6a4b1-337">将 /hub 路由映射到 `ChatHub` 中心  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-337">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="6a4b1-338">在 `Startup.Configure` 方法的末尾添加以下行：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-338">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="6a4b1-339">在项目根中创建名为 Hubs 的新目录  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-339">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="6a4b1-340">目的是存储 SignalR 中心（在下一步中创建）。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-340">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="6a4b1-341">创建包含以下代码的中心 Hubs/ChatHub.cs  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-341">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="6a4b1-342">在 Startup.cs 文件顶部添加以下代码，解析 `ChatHub` 引用  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-342">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="6a4b1-343">启用客户端和服务器通信</span><span class="sxs-lookup"><span data-stu-id="6a4b1-343">Enable client and server communication</span></span>

<span data-ttu-id="6a4b1-344">应用当前显示一个发送消息的简单窗体。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-344">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="6a4b1-345">尝试执行此操作时没有任何反应。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-345">Nothing happens when you try to do so.</span></span> <span data-ttu-id="6a4b1-346">服务器正在侦听特定的路由，但是不涉及发送消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-346">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="6a4b1-347">在项目根执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-347">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="6a4b1-348">前面的命令将安装 [SignalR TypeScript 客户端](https://www.npmjs.com/package/@microsoft/signalr)，它允许客户端向服务器发送消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-348">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="6a4b1-349">将突出显示的代码添加到 src/index.ts 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-349">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="6a4b1-350">前面的代码支持从服务器接收消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-350">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="6a4b1-351">`HubConnectionBuilder` 类创建新的生成器，用于配置服务器连接。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-351">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="6a4b1-352">`withUrl` 函数配置中心 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-352">The `withUrl` function configures the hub URL.</span></span>

    SignalR<span data-ttu-id="6a4b1-353"> 启用客户端和服务器之间的消息交换。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-353"> enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="6a4b1-354">每个消息都有特定的名称。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-354">Each message has a specific name.</span></span> <span data-ttu-id="6a4b1-355">例如，名为 `messageReceived` 的消息可以执行负责在消息区域显示新消息的逻辑。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-355">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="6a4b1-356">可以通过 `on` 函数完成对特定消息的侦听。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-356">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="6a4b1-357">可以侦听任意数量的消息名称。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-357">You can listen to any number of message names.</span></span> <span data-ttu-id="6a4b1-358">还可以将参数传递到消息，例如所接收消息的作者姓名和内容。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-358">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="6a4b1-359">客户端收到一条消息后，会创建一个新的 `div` 元素并在其 `innerHTML` 属性中显示作者姓名和消息内容。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-359">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="6a4b1-360">它添加到显示消息的主要 `div` 元素。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-360">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="6a4b1-361">客户端可以接收消息后，将它配置为发送消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-361">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="6a4b1-362">将突出显示的代码添加到 src/index.ts 文件  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-362">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="6a4b1-363">通过 WebSockets 连接发送消息需要调用 `send` 方法。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-363">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="6a4b1-364">该方法的第一个参数是消息名称。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-364">The method's first parameter is the message name.</span></span> <span data-ttu-id="6a4b1-365">消息数据包含其他参数。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-365">The message data inhabits the other parameters.</span></span> <span data-ttu-id="6a4b1-366">在此示例中，一条标识为 `newMessage` 的消息已发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-366">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="6a4b1-367">该消息包含用户名和文本框中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-367">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="6a4b1-368">如果发送成功，会清空文本框。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-368">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="6a4b1-369">将突出显示的方法添加到 `ChatHub` 类：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-369">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="6a4b1-370">服务器收到消息后，前面的代码会将这些消息播发到所有连接的用户。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-370">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="6a4b1-371">没有必要使用泛型 `on` 方法接收所有消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-371">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="6a4b1-372">使用以消息名称命名的方法就可以了。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-372">A method named after the message name suffices.</span></span>

    <span data-ttu-id="6a4b1-373">在此示例中，TypeScript 客户端发送一条标识为 `newMessage` 的消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-373">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="6a4b1-374">C# `NewMessage` 方法需要客户端发送的数据。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-374">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="6a4b1-375">在 [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) 调用 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 方法。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-375">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="6a4b1-376">接收的消息会发送到所有连接到中心的客户端。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-376">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="6a4b1-377">测试应用</span><span class="sxs-lookup"><span data-stu-id="6a4b1-377">Test the app</span></span>

<span data-ttu-id="6a4b1-378">确认应用遵循以下步骤。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-378">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6a4b1-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a4b1-379">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6a4b1-380">在 release 模式下运行 Webpack  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-380">Run Webpack in *release* mode.</span></span> <span data-ttu-id="6a4b1-381">使用“包管理器控制台”窗口，在项目根中运行以下命令  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-381">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="6a4b1-382">如果不在项目根中，请在输入该命令之前输入 `cd SignalRWebPack`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-382">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="6a4b1-383">选择“调试” > “开始执行(不调试)”，在不附加调试器的情况下在浏览器中启动应用   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-383">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="6a4b1-384">在 `http://localhost:<port_number>` 上提供 *wwwroot/index.html* 文件。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-384">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="6a4b1-385">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-385">Open another browser instance (any browser).</span></span> <span data-ttu-id="6a4b1-386">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-386">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6a4b1-387">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-387">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="6a4b1-388">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-388">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6a4b1-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a4b1-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="6a4b1-390">通过在项目根中执行以下命令以 release 模式运行 Webpack  ：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-390">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="6a4b1-391">通过在项目根中执行以下命令生成和运行应用：</span><span class="sxs-lookup"><span data-stu-id="6a4b1-391">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="6a4b1-392">Web 服务器启动应用并在 localhost 上提供。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-392">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="6a4b1-393">打开浏览器，转到 `http://localhost:<port_number>`。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-393">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="6a4b1-394">提供 wwwroot/index.html 文件  。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-394">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="6a4b1-395">从地址栏复制 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-395">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="6a4b1-396">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-396">Open another browser instance (any browser).</span></span> <span data-ttu-id="6a4b1-397">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-397">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="6a4b1-398">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮   。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-398">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="6a4b1-399">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="6a4b1-399">The unique user name and message are displayed on both pages instantly.</span></span>

---

![两个浏览器窗口都显示的消息](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6a4b1-401">其他资源</span><span class="sxs-lookup"><span data-stu-id="6a4b1-401">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
