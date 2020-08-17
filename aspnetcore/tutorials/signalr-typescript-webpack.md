---
title: 配合使用 ASP.NET Core SignalR 和 TypeScript 以及 Webpack
author: ssougnez
description: 在本教程中，我们将配置 Webpack，以捆绑和生成 ASP.NET Core SignalR Web 应用，该应用的客户端是使用 TypeScript 编写的。
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 48b59fea5da3872fb29cacd9edbedd14de9e602f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019412"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="7dcd4-103">配合使用 ASP.NET Core SignalR 和 TypeScript 以及 Webpack</span><span class="sxs-lookup"><span data-stu-id="7dcd4-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="7dcd4-104">作者：[Sébastien Sougnez](https://twitter.com/ssougnez) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7dcd4-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7dcd4-105">开发人员可以通过 [Webpack](https://webpack.js.org/) 捆绑和生成 Web 应用的客户端资源。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="7dcd4-106">本教程介绍在 ASP.NET Core SignalR Web 应用中使用 Webpack，该应用的客户端是使用 [TypeScript](https://www.typescriptlang.org/) 编写的。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="7dcd4-107">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7dcd4-108">为入门 ASP.NET Core SignalR 应用搭建基架</span><span class="sxs-lookup"><span data-stu-id="7dcd4-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="7dcd4-109">配置 SignalR TypeScript 客户端</span><span class="sxs-lookup"><span data-stu-id="7dcd4-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="7dcd4-110">使用 Webpack 配置生成管道</span><span class="sxs-lookup"><span data-stu-id="7dcd4-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="7dcd4-111">配置 SignalR 服务器</span><span class="sxs-lookup"><span data-stu-id="7dcd4-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="7dcd4-112">启用客户端和服务器之间的通信</span><span class="sxs-lookup"><span data-stu-id="7dcd4-112">Enable communication between client and server</span></span>

<span data-ttu-id="7dcd4-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7dcd4-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="7dcd4-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="7dcd4-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7dcd4-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dcd4-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7dcd4-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="7dcd4-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7dcd4-117">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7dcd4-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="7dcd4-118">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="7dcd4-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7dcd4-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7dcd4-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7dcd4-121">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7dcd4-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7dcd4-122">适用于 Visual Studio Code 的 C# 版本 1.17.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7dcd4-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="7dcd4-123">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="7dcd4-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="7dcd4-124">创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="7dcd4-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7dcd4-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dcd4-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7dcd4-126">配置 Visual Studio，在 PATH 环境变量中查找 npm。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="7dcd4-127">默认情况下，Visual Studio 使用在安装目录中找到的 npm 版本。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="7dcd4-128">在 Visual Studio 中按照以下说明执行操作：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="7dcd4-129">启动 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-129">Launch Visual Studio.</span></span> <span data-ttu-id="7dcd4-130">在“启动”窗口中，选择“继续但无需代码”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="7dcd4-131">导航到“工具”>“选项”>“项目和解决方案”>“Web 包管理”>“外部 Web 工具”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="7dcd4-132">在列表中选择 $(PATH) 项。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="7dcd4-133">单击向上键将项移动列表中的第二个位置，然后选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Visual Studio 配置](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="7dcd4-135">Visual Studio 配置完成。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="7dcd4-136">使用“文件” > “新建” > “项目”菜单选项，然后选择“ASP.NET Core Web 应用程序”模板   。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="7dcd4-137">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-137">Select **Next**.</span></span>
1. <span data-ttu-id="7dcd4-138">将项目命名为 SignalRWebPack 并选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="7dcd4-139">从目标框架下拉列表选择 .NET Core 并从框架选择器下拉列表选择 ASP.NET Core 3.1 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="7dcd4-140">选择“空白”模板并选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="7dcd4-141">将 `Microsoft.TypeScript.MSBuild` 包添加到项目:</span><span class="sxs-lookup"><span data-stu-id="7dcd4-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="7dcd4-142">在“解决方案资源管理器”（右侧窗格）中，右键单击项目节点，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="7dcd4-143">在“浏览”选项卡中，搜索 `Microsoft.TypeScript.MSBuild`，然后单击右侧的“安装”来安装包 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="7dcd4-144">Visual Studio 会将 NuGet 包添加到解决方案资源管理器中的“依赖项”节点下，从而在项目中启用 TypeScript 编译 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7dcd4-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7dcd4-146">在“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="7dcd4-147">`dotnet new` 命令会在 SignalRWebPack 目录中创建一个空的 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="7dcd4-148">`code` 命令会在 Visual Studio Code 的当前实例中打开 SignalRWebPack 文件夹。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="7dcd4-149">在“集成终端”中运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="7dcd4-150">上述命令将添加 [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) 包，从而在项目中启用 TypeScript 编译。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="7dcd4-151">配置 Webpack 和 TypeScript</span><span class="sxs-lookup"><span data-stu-id="7dcd4-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="7dcd4-152">以下步骤配置 TypeScript 到 JavaScript 的转换和客户端资源的捆绑。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="7dcd4-153">在项目根目录中运行以下命令，创建 package.json 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="7dcd4-154">将突出显示的属性添加到 package.json 文件并保存文件更改：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="7dcd4-155">将 `private` 属性设置为 `true`，防止下一步出现包安装警告。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="7dcd4-156">安装所需的 npm 包。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-156">Install the required npm packages.</span></span> <span data-ttu-id="7dcd4-157">从项目根目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="7dcd4-158">需要注意的一些命令细节：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-158">Some command details to note:</span></span>

    * <span data-ttu-id="7dcd4-159">每个包名称中 `@` 符号后是版本号。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="7dcd4-160">npm 安装这些特定的包版本。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="7dcd4-161">`-E` 选项禁用 npm 将[语义化版本控制](https://semver.org/)范围运算符写到 package.json 的默认行为。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="7dcd4-162">例如，使用 `"webpack": "4.41.5"` 而不是 `"webpack": "^4.41.5"`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="7dcd4-163">此选项防止意外升级到新的包版本。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="7dcd4-164">有关详细信息，请参阅 [npm-install](https://docs.npmjs.com/cli/install) 文档。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="7dcd4-165">将 package.json 文件的 `scripts` 属性替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="7dcd4-166">脚本的一些解释：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="7dcd4-167">`build`：在开发模式下捆绑客户端资源并观察文件更改。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="7dcd4-168">文件观察程序使捆绑在每次项目文件发生更改时重新生成。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="7dcd4-169">`mode` 选项可禁用生产优化，例如摇树优化和缩小优化。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="7dcd4-170">仅在开发中使用 `build`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="7dcd4-171">`release`：在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="7dcd4-172">`publish`：运行 `release` 脚本，在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="7dcd4-173">它调用 .NET Core CLI 的 [publish](/dotnet/core/tools/dotnet-publish) 命令发布应用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="7dcd4-174">在项目根目录中创建名为 webpack.config.js 的文件，使其包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="7dcd4-175">前面的文件配置 Webpack 编译。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="7dcd4-176">需要注意的一些配置细节：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="7dcd4-177">`output` 属性替代 dist 的默认值。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="7dcd4-178">捆绑反而在 wwwroot 目录中发出。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="7dcd4-179">`resolve.extensions` 数组包含 .js，以便导入 SignalR 客户端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="7dcd4-180">在项目根目录中创建新的 src 目录，以存储项目的客户端资产。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="7dcd4-181">创建包含以下标记的 src/index.html。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="7dcd4-182">前面的 HTML 定义主页的样板标记。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="7dcd4-183">创建新的 src/css 目录。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="7dcd4-184">目的是存储项目的 .css 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="7dcd4-185">创建包含以下 CSS 的 src/css/main.css：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="7dcd4-186">前面的 main.css 文件设计应用样式。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="7dcd4-187">创建包含以下 JSON 的 src/tsconfig.json：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="7dcd4-188">前面的代码配置 TypeScript 编译器，生成与 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 兼容的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="7dcd4-189">创建包含以下代码的 src/index.ts：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="7dcd4-190">前面的 TypeScript 检索对 DOM 元素的引用并附加两个事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="7dcd4-191">`keyup`：用户在 `tbMessage` 文本框中键入时触发此事件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="7dcd4-192">用户按 Enter 时调用 `send` 函数。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="7dcd4-193">`click`：用户单击“发送”按钮时触发此事件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="7dcd4-194">调用 `send` 函数。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="7dcd4-195">配置应用</span><span class="sxs-lookup"><span data-stu-id="7dcd4-195">Configure the app</span></span>

1. <span data-ttu-id="7dcd4-196">在 `Startup.Configure` 中，添加对 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 和 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="7dcd4-197">上述代码允许服务器查找和处理 index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="7dcd4-198">无论用户输入完整 URL 还是 Web 应用的根 URL，都会提供该文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="7dcd4-199">在 `Startup.Configure` 的末尾，将 /hub 路由映射到 `ChatHub` 中心。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="7dcd4-200">将显示 Hello World! 的代码</span><span class="sxs-lookup"><span data-stu-id="7dcd4-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="7dcd4-201">替换为以下行：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="7dcd4-202">在 `Startup.ConfigureServices` 中调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="7dcd4-203">在项目根目录 SignalRWebPack/ 中创建名为 Hubs 的新目录，以存储 SignalR 中心 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="7dcd4-204">创建包含以下代码的中心 Hubs/ChatHub.cs：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="7dcd4-205">在 Startup.cs 文件顶部添加以下 `using` 语句来解析 `ChatHub` 引用：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="7dcd4-206">启用客户端和服务器通信</span><span class="sxs-lookup"><span data-stu-id="7dcd4-206">Enable client and server communication</span></span>

<span data-ttu-id="7dcd4-207">应用目前显示用于发送消息的基本窗体，但尚不能正常工作。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="7dcd4-208">服务器正在侦听特定的路由，但是不涉及发送消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="7dcd4-209">在项目根目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="7dcd4-210">上述的代码会安装：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-210">The preceding command installs:</span></span>

     * <span data-ttu-id="7dcd4-211">[SignalR TypeScript 客户端](https://www.npmjs.com/package/@microsoft/signalr)，它允许客户端向服务器发送消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="7dcd4-212">用于 node.js 的 TypeScript 类型定义，支持 Node.js 类型的编译时检查。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="7dcd4-213">将突出显示的代码添加到 src/index.ts 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="7dcd4-214">前面的代码支持从服务器接收消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="7dcd4-215">`HubConnectionBuilder` 类创建新的生成器，用于配置服务器连接。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="7dcd4-216">`withUrl` 函数配置中心 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="7dcd4-217">SignalR 启用客户端和服务器之间的消息交换。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="7dcd4-218">每个消息都有特定的名称。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-218">Each message has a specific name.</span></span> <span data-ttu-id="7dcd4-219">例如，名为 `messageReceived` 的消息可以运行负责在消息区域显示新消息的逻辑。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="7dcd4-220">可以通过 `on` 函数完成对特定消息的侦听。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="7dcd4-221">可以侦听任意数量的消息名称。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="7dcd4-222">还可以将参数传递到消息，例如所接收消息的作者姓名和内容。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="7dcd4-223">客户端收到一条消息后，会创建一个新的 `div` 元素并在其 `innerHTML` 属性中显示作者姓名和消息内容。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="7dcd4-224">它添加到显示消息的主要 `div` 元素。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="7dcd4-225">客户端可以接收消息后，将它配置为发送消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="7dcd4-226">将突出显示的代码添加到 src/index.ts 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="7dcd4-227">通过 WebSockets 连接发送消息需要调用 `send` 方法。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="7dcd4-228">该方法的第一个参数是消息名称。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="7dcd4-229">消息数据包含其他参数。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="7dcd4-230">在此示例中，一条标识为 `newMessage` 的消息已发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="7dcd4-231">该消息包含用户名和文本框中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="7dcd4-232">如果发送成功，会清空文本框。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="7dcd4-233">将 `NewMessage` 方法添加到 `ChatHub` 类：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="7dcd4-234">服务器收到消息后，前面的代码会将这些消息播发到所有连接的用户。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="7dcd4-235">没有必要使用泛型 `on` 方法接收所有消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="7dcd4-236">使用以消息名称命名的方法就可以了。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="7dcd4-237">在此示例中，TypeScript 客户端发送一条标识为 `newMessage` 的消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="7dcd4-238">C# `NewMessage` 方法需要客户端发送的数据。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="7dcd4-239">在 [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) 上对 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 进行调用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="7dcd4-240">接收的消息会发送到所有连接到中心的客户端。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="7dcd4-241">测试应用</span><span class="sxs-lookup"><span data-stu-id="7dcd4-241">Test the app</span></span>

<span data-ttu-id="7dcd4-242">确认应用遵循以下步骤。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7dcd4-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dcd4-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7dcd4-244">在 release 模式下运行 Webpack。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="7dcd4-245">使用“包管理器控制台”窗口，在项目根目录中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="7dcd4-246">如果不在项目根中，请在输入该命令之前输入 `cd SignalRWebPack`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="7dcd4-247">选择“调试” > “开始执行(不调试)”，在不附加调试器的情况下在浏览器中启动应用 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="7dcd4-248">在 `http://localhost:<port_number>` 上提供 *wwwroot/index.html* 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="7dcd4-249">如果遇到编译错误，请尝试关闭并重新打开解决方案。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="7dcd4-250">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="7dcd4-251">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="7dcd4-252">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="7dcd4-253">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7dcd4-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7dcd4-255">通过在项目根中执行以下命令以 release 模式运行 Webpack：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="7dcd4-256">通过在项目根中执行以下命令生成和运行应用：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="7dcd4-257">Web 服务器启动应用并在 localhost 上提供。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="7dcd4-258">打开浏览器，转到 `http://localhost:<port_number>`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="7dcd4-259">提供 wwwroot/index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="7dcd4-260">从地址栏复制 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="7dcd4-261">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="7dcd4-262">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="7dcd4-263">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="7dcd4-264">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![两个浏览器窗口都显示的消息](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="7dcd4-266">先决条件</span><span class="sxs-lookup"><span data-stu-id="7dcd4-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7dcd4-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dcd4-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7dcd4-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="7dcd4-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7dcd4-269">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7dcd4-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="7dcd4-270">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="7dcd4-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7dcd4-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7dcd4-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7dcd4-273">.NET Core SDK 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7dcd4-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7dcd4-274">适用于 Visual Studio Code 的 C# 版本 1.17.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7dcd4-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="7dcd4-275">带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)</span><span class="sxs-lookup"><span data-stu-id="7dcd4-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="7dcd4-276">创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="7dcd4-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7dcd4-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dcd4-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7dcd4-278">配置 Visual Studio，在 PATH 环境变量中查找 npm。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="7dcd4-279">默认情况下，Visual Studio 使用在安装目录中找到的 npm 版本。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="7dcd4-280">在 Visual Studio 中按照以下说明执行操作：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="7dcd4-281">导航到“工具”>“选项”>“项目和解决方案”>“Web 包管理”>“外部 Web 工具”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="7dcd4-282">在列表中选择 $(PATH) 项。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="7dcd4-283">单击向上键将项移动列表第二个位置。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio 配置](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="7dcd4-285">已完成 Visual Studio 配置。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="7dcd4-286">可以开始创建项目了。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-286">It's time to create the project.</span></span>

1. <span data-ttu-id="7dcd4-287">使用“文件”>“新建”>“项目”菜单选项，然后选择“ASP.NET Core Web 应用程序”模板   。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="7dcd4-288">将项目命名为 SignalRWebPack 并选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="7dcd4-289">从目标框架下拉列表选择 .NET Core 并从框架选择器下拉列表选择 ASP.NET Core 2.2 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="7dcd4-290">选择“空白”模板并选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7dcd4-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7dcd4-292">在“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="7dcd4-293">SignalRWebPack 目录中创建了一个面向 .NET Core 的空 ASP.NET Core Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="7dcd4-294">配置 Webpack 和 TypeScript</span><span class="sxs-lookup"><span data-stu-id="7dcd4-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="7dcd4-295">以下步骤配置 TypeScript 到 JavaScript 的转换和客户端资源的捆绑。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="7dcd4-296">在项目根目录中运行以下命令，创建 package.json 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="7dcd4-297">将突出显示的属性添加到 package.json 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="7dcd4-298">将 `private` 属性设置为 `true`，防止下一步出现包安装警告。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="7dcd4-299">安装所需的 npm 包。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-299">Install the required npm packages.</span></span> <span data-ttu-id="7dcd4-300">从项目根目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="7dcd4-301">需要注意的一些命令细节：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-301">Some command details to note:</span></span>

    * <span data-ttu-id="7dcd4-302">每个包名称中 `@` 符号后是版本号。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="7dcd4-303">npm 安装这些特定的包版本。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="7dcd4-304">`-E` 选项禁用 npm 将[语义化版本控制](https://semver.org/)范围运算符写到 package.json 的默认行为。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="7dcd4-305">例如，使用 `"webpack": "4.29.3"` 而不是 `"webpack": "^4.29.3"`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="7dcd4-306">此选项防止意外升级到新的包版本。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="7dcd4-307">有关详细信息，请参阅 [npm-install](https://docs.npmjs.com/cli/install) 文档。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="7dcd4-308">将 package.json 文件的 `scripts` 属性替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="7dcd4-309">脚本的一些解释：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="7dcd4-310">`build`：在开发模式下捆绑客户端资源并观察文件更改。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="7dcd4-311">文件观察程序使捆绑在每次项目文件发生更改时重新生成。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="7dcd4-312">`mode` 选项可禁用生产优化，例如摇树优化和缩小优化。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="7dcd4-313">仅在开发中使用 `build`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="7dcd4-314">`release`：在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="7dcd4-315">`publish`：运行 `release` 脚本，在生产模式下捆绑客户端资源。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="7dcd4-316">它调用 .NET Core CLI 的 [publish](/dotnet/core/tools/dotnet-publish) 命令发布应用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="7dcd4-317">在项目根目录中创建名为 webpack.config.js 的文件，使其包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="7dcd4-318">前面的文件配置 Webpack 编译。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="7dcd4-319">需要注意的一些配置细节：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="7dcd4-320">`output` 属性替代 dist 的默认值。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="7dcd4-321">捆绑反而在 wwwroot 目录中发出。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="7dcd4-322">`resolve.extensions` 数组包含 .js，以便导入 SignalR 客户端 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="7dcd4-323">在项目根目录中创建新的 src 目录，以存储项目的客户端资产。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="7dcd4-324">创建包含以下标记的 src/index.html。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="7dcd4-325">前面的 HTML 定义主页的样板标记。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="7dcd4-326">创建新的 src/css 目录。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="7dcd4-327">目的是存储项目的 .css 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="7dcd4-328">创建包含以下标记的 src/css/main.css：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="7dcd4-329">前面的 main.css 文件设计应用样式。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="7dcd4-330">创建包含以下 JSON 的 src/tsconfig.json：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="7dcd4-331">前面的代码配置 TypeScript 编译器，生成与 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 兼容的 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="7dcd4-332">创建包含以下代码的 src/index.ts：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="7dcd4-333">前面的 TypeScript 检索对 DOM 元素的引用并附加两个事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="7dcd4-334">`keyup`：用户在 `tbMessage` 文本框中键入时触发此事件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="7dcd4-335">用户按 Enter 时调用 `send` 函数。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="7dcd4-336">`click`：用户单击“发送”按钮时触发此事件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="7dcd4-337">调用 `send` 函数。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="7dcd4-338">配置 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="7dcd4-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="7dcd4-339">`Startup.Configure` 方法中提供的代码显示 Hello World!。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="7dcd4-340">将 `app.Run` 方法调用替换为对 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 和 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="7dcd4-341">前面的代码允许服务器定位并提供 index.html 文件，无论用户输入完整 URL 还是 Web 应用的根 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="7dcd4-342">在 `Startup.ConfigureServices` 中调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7dcd4-343">此操作会将 SignalR 服务添加到项目。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="7dcd4-344">将 /hub 路由映射到 `ChatHub` 中心。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="7dcd4-345">在 `Startup.Configure` 的末尾添加以下行：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="7dcd4-346">在项目根中创建名为 Hubs 的新目录。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="7dcd4-347">目的是存储 SignalR 中心（在下一步中创建）。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="7dcd4-348">创建包含以下代码的中心 Hubs/ChatHub.cs：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="7dcd4-349">在 Startup.cs 文件顶部添加以下代码，解析 `ChatHub` 引用：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="7dcd4-350">启用客户端和服务器通信</span><span class="sxs-lookup"><span data-stu-id="7dcd4-350">Enable client and server communication</span></span>

<span data-ttu-id="7dcd4-351">应用当前显示一个发送消息的简单窗体。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="7dcd4-352">尝试执行此操作时没有任何反应。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="7dcd4-353">服务器正在侦听特定的路由，但是不涉及发送消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="7dcd4-354">在项目根目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="7dcd4-355">前面的命令将安装 [SignalR TypeScript 客户端](https://www.npmjs.com/package/@microsoft/signalr)，它允许客户端向服务器发送消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="7dcd4-356">将突出显示的代码添加到 src/index.ts 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="7dcd4-357">前面的代码支持从服务器接收消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="7dcd4-358">`HubConnectionBuilder` 类创建新的生成器，用于配置服务器连接。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="7dcd4-359">`withUrl` 函数配置中心 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="7dcd4-360">SignalR 启用客户端和服务器之间的消息交换。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="7dcd4-361">每个消息都有特定的名称。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-361">Each message has a specific name.</span></span> <span data-ttu-id="7dcd4-362">例如，名为 `messageReceived` 的消息可以运行负责在消息区域显示新消息的逻辑。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="7dcd4-363">可以通过 `on` 函数完成对特定消息的侦听。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="7dcd4-364">可以侦听任意数量的消息名称。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-364">You can listen to any number of message names.</span></span> <span data-ttu-id="7dcd4-365">还可以将参数传递到消息，例如所接收消息的作者姓名和内容。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="7dcd4-366">客户端收到一条消息后，会创建一个新的 `div` 元素并在其 `innerHTML` 属性中显示作者姓名和消息内容。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="7dcd4-367">新消息将添加到显示消息的主 `div` 元素中。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="7dcd4-368">客户端可以接收消息后，将它配置为发送消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="7dcd4-369">将突出显示的代码添加到 src/index.ts 文件：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="7dcd4-370">通过 WebSockets 连接发送消息需要调用 `send` 方法。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="7dcd4-371">该方法的第一个参数是消息名称。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="7dcd4-372">消息数据包含其他参数。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="7dcd4-373">在此示例中，一条标识为 `newMessage` 的消息已发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="7dcd4-374">该消息包含用户名和文本框中的用户输入。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="7dcd4-375">如果发送成功，会清空文本框。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="7dcd4-376">将 `NewMessage` 方法添加到 `ChatHub` 类：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="7dcd4-377">服务器收到消息后，前面的代码会将这些消息播发到所有连接的用户。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="7dcd4-378">没有必要使用泛型 `on` 方法接收所有消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="7dcd4-379">使用以消息名称命名的方法就可以了。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="7dcd4-380">在此示例中，TypeScript 客户端发送一条标识为 `newMessage` 的消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="7dcd4-381">C# `NewMessage` 方法需要客户端发送的数据。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="7dcd4-382">在 [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) 上对 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 进行调用。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="7dcd4-383">接收的消息会发送到所有连接到中心的客户端。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="7dcd4-384">测试应用</span><span class="sxs-lookup"><span data-stu-id="7dcd4-384">Test the app</span></span>

<span data-ttu-id="7dcd4-385">确认应用遵循以下步骤。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7dcd4-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dcd4-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7dcd4-387">在 release 模式下运行 Webpack。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="7dcd4-388">使用“包管理器控制台”窗口，在项目根目录中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="7dcd4-389">如果不在项目根中，请在输入该命令之前输入 `cd SignalRWebPack`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="7dcd4-390">选择“调试” > “开始执行(不调试)”，在不附加调试器的情况下在浏览器中启动应用 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="7dcd4-391">在 `http://localhost:<port_number>` 上提供 *wwwroot/index.html* 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="7dcd4-392">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="7dcd4-393">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="7dcd4-394">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="7dcd4-395">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7dcd4-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7dcd4-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7dcd4-397">通过在项目根中执行以下命令以 release 模式运行 Webpack：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="7dcd4-398">通过在项目根中执行以下命令生成和运行应用：</span><span class="sxs-lookup"><span data-stu-id="7dcd4-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="7dcd4-399">Web 服务器启动应用并在 localhost 上提供。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="7dcd4-400">打开浏览器，转到 `http://localhost:<port_number>`。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="7dcd4-401">提供 wwwroot/index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="7dcd4-402">从地址栏复制 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="7dcd4-403">打开另一个浏览器实例（任意浏览器）。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="7dcd4-404">在地址栏中粘贴 URL。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="7dcd4-405">选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="7dcd4-406">两个页面上立即显示唯一的用户名和消息。</span><span class="sxs-lookup"><span data-stu-id="7dcd4-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![两个浏览器窗口都显示的消息](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7dcd4-408">其他资源</span><span class="sxs-lookup"><span data-stu-id="7dcd4-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
