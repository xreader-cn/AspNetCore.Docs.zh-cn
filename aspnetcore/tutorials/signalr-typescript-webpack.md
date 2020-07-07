---
title: 配合使用 ASP.NET Core SignalR 和 TypeScript 以及 Webpack
author: ssougnez
description: 在本教程中，我们将配置 Webpack，以捆绑和生成 ASP.NET Core SignalR Web 应用，该应用的客户端是使用 TypeScript 编写的。
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 49d185ce941d5f8e841224e7de3b72b8350a1c47
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407897"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>配合使用 ASP.NET Core SignalR 和 TypeScript 以及 Webpack

作者：[Sébastien Sougnez](https://twitter.com/ssougnez) 和 [Scott Addie](https://twitter.com/Scott_Addie)

开发人员可以通过 [Webpack](https://webpack.js.org/) 捆绑和生成 Web 应用的客户端资源。 本教程介绍在 ASP.NET Core SignalR Web 应用中使用 Webpack，该应用的客户端是使用 [TypeScript](https://www.typescriptlang.org/) 编写的。

在本教程中，你将了解：

> [!div class="checklist"]
> * 为入门 ASP.NET Core SignalR 应用搭建基架
> * 配置 SignalR TypeScript 客户端
> * 使用 Webpack 配置生成管道
> * 配置 SignalR 服务器
> * 启用客户端和服务器之间的通信

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample)（[如何下载](xref:index#how-to-download-a-sample)）

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载
* [.NET Core SDK 3.0 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* 带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 3.0 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [适用于 Visual Studio Code 的 C# 版本 1.17.1 或更高版本](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* 带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)

---

## <a name="create-the-aspnet-core-web-app"></a>创建 ASP.NET Core Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

配置 Visual Studio，在 PATH 环境变量中查找 npm。 默认情况下，Visual Studio 使用在安装目录中找到的 npm 版本。 在 Visual Studio 中按照以下说明执行操作：

1. 启动 Visual Studio。 在“启动”窗口中，选择“继续但无需代码”。
1. 导航到“工具”>“选项”>“项目和解决方案”>“Web 包管理”>“外部 Web 工具”。
1. 在列表中选择 $(PATH) 项。 单击向上键将项移动列表中的第二个位置，然后选择“确定”。

    ![Visual Studio 配置](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Visual Studio 配置完成。

1. 使用“文件” > “新建” > “项目”菜单选项，然后选择“ASP.NET Core Web 应用程序”模板   。 选择“下一步”。
1. 将项目命名为 SignalRWebPack 并选择“创建”。
1. 从目标框架下拉列表选择 .NET Core 并从框架选择器下拉列表选择 ASP.NET Core 3.1 。 选择“空白”模板并选择“创建” 。

将 `Microsoft.TypeScript.MSBuild` 包添加到项目:

1. 在“解决方案资源管理器”（右侧窗格）中，右键单击项目节点，然后选择“管理 NuGet 包” 。 在“浏览”选项卡中，搜索 `Microsoft.TypeScript.MSBuild`，然后单击右侧的“安装”来安装包 。

Visual Studio 会将 NuGet 包添加到解决方案资源管理器中的“依赖项”节点下，从而在项目中启用 TypeScript 编译 。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在“集成终端”中运行以下命令：

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* `dotnet new` 命令会在 SignalRWebPack 目录中创建一个空的 ASP.NET Core Web 应用。
* `code` 命令会在 Visual Studio Code 的当前实例中打开 SignalRWebPack 文件夹。

在“集成终端”中运行以下 .NET Core CLI 命令：

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

上述命令将添加 [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) 包，从而在项目中启用 TypeScript 编译。

---

## <a name="configure-webpack-and-typescript"></a>配置 Webpack 和 TypeScript

以下步骤配置 TypeScript 到 JavaScript 的转换和客户端资源的捆绑。

1. 在项目根目录中运行以下命令，创建 package.json 文件：

    ```console
    npm init -y
    ```

1. 将突出显示的属性添加到 package.json 文件并保存文件更改：

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    将 `private` 属性设置为 `true`，防止下一步出现包安装警告。

1. 安装所需的 npm 包。 从项目根目录运行以下命令：

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    需要注意的一些命令细节：

    * 每个包名称中 `@` 符号后是版本号。 npm 安装这些特定的包版本。
    * `-E` 选项禁用 npm 将[语义化版本控制](https://semver.org/)范围运算符写到 package.json 的默认行为。 例如，使用 `"webpack": "4.41.5"` 而不是 `"webpack": "^4.41.5"`。 此选项防止意外升级到新的包版本。

    有关详细信息，请参阅 [npm-install](https://docs.npmjs.com/cli/install) 文档。

1. 将 package.json 文件的 `scripts` 属性替换为以下代码：

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    脚本的一些解释：

    * `build`：在开发模式下捆绑客户端资源并观察文件更改。 文件观察程序使捆绑在每次项目文件发生更改时重新生成。 `mode` 选项可禁用生产优化，例如摇树优化和缩小优化。 仅在开发中使用 `build`。
    * `release`：在生产模式下捆绑客户端资源。
    * `publish`：运行 `release` 脚本，在生产模式下捆绑客户端资源。 它调用 .NET Core CLI 的 [publish](/dotnet/core/tools/dotnet-publish) 命令发布应用。

1. 在项目根目录中创建名为 webpack.config.js 的文件，使其包含以下代码：

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    前面的文件配置 Webpack 编译。 需要注意的一些配置细节：

    * `output` 属性替代 dist 的默认值。 捆绑反而在 wwwroot 目录中发出。
    * `resolve.extensions` 数组包含 .js，以便导入 SignalR 客户端 JavaScript。

1. 在项目根目录中创建新的 src 目录，以存储项目的客户端资产。

1. 创建包含以下标记的 src/index.html。

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    前面的 HTML 定义主页的样板标记。

1. 创建新的 src/css 目录。 目的是存储项目的 .css 文件。

1. 创建包含以下 CSS 的 src/css/main.css：

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    前面的 main.css 文件设计应用样式。

1. 创建包含以下 JSON 的 src/tsconfig.json：

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    前面的代码配置 TypeScript 编译器，生成与 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 兼容的 JavaScript。

1. 创建包含以下代码的 src/index.ts：

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    前面的 TypeScript 检索对 DOM 元素的引用并附加两个事件处理程序：

    * `keyup`：用户在 `tbMessage` 文本框中键入时触发此事件。 用户按 Enter 时调用 `send` 函数。
    * `click`：用户单击“发送”按钮时触发此事件。 调用 `send` 函数。

## <a name="configure-the-app"></a>配置应用

1. 在 `Startup.Configure` 中，添加对 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 和 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   上述代码允许服务器查找和处理 index.html 文件。  无论用户输入完整 URL 还是 Web 应用的根 URL，都会提供该文件。

1. 在 `Startup.Configure` 的末尾，将 /hub 路由映射到 `ChatHub` 中心。 将显示 Hello World! 的代码 替换为以下行： 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. 在 `Startup.ConfigureServices`中，调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)。

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. 在项目根目录 SignalRWebPack/ 中创建名为 Hubs 的新目录，以存储 SignalR 中心 。

1. 创建包含以下代码的中心 Hubs/ChatHub.cs：

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. 在 Startup.cs 文件顶部添加以下 `using` 语句来解析 `ChatHub` 引用：

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>启用客户端和服务器通信

应用目前显示用于发送消息的基本窗体，但尚不能正常工作。 服务器正在侦听特定的路由，但是不涉及发送消息。

1. 在项目根目录运行以下命令：

    ```console
    npm i @microsoft/signalr @types/node
    ```

    上述的代码会安装：

     * [SignalR TypeScript 客户端](https://www.npmjs.com/package/@microsoft/signalr)，它允许客户端向服务器发送消息。
     * 用于 node.js 的 TypeScript 类型定义，支持 Node.js 类型的编译时检查。

1. 将突出显示的代码添加到 src/index.ts 文件：

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    前面的代码支持从服务器接收消息。 `HubConnectionBuilder` 类创建新的生成器，用于配置服务器连接。 `withUrl` 函数配置中心 URL。

    SignalR 启用客户端和服务器之间的消息交换。 每个消息都有特定的名称。 例如，名为 `messageReceived` 的消息可以运行负责在消息区域显示新消息的逻辑。 可以通过 `on` 函数完成对特定消息的侦听。 可以侦听任意数量的消息名称。 还可以将参数传递到消息，例如所接收消息的作者姓名和内容。 客户端收到一条消息后，会创建一个新的 `div` 元素并在其 `innerHTML` 属性中显示作者姓名和消息内容。 它添加到显示消息的主要 `div` 元素。

1. 客户端可以接收消息后，将它配置为发送消息。 将突出显示的代码添加到 src/index.ts 文件：

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    通过 WebSockets 连接发送消息需要调用 `send` 方法。 该方法的第一个参数是消息名称。 消息数据包含其他参数。 在此示例中，一条标识为 `newMessage` 的消息已发送到服务器。 该消息包含用户名和文本框中的用户输入。 如果发送成功，会清空文本框。

1. 将 `NewMessage` 方法添加到 `ChatHub` 类：

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    服务器收到消息后，前面的代码会将这些消息播发到所有连接的用户。 没有必要使用泛型 `on` 方法接收所有消息。 使用以消息名称命名的方法就可以了。

    在此示例中，TypeScript 客户端发送一条标识为 `newMessage` 的消息。 C# `NewMessage` 方法需要客户端发送的数据。 在 [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) 上对 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 进行调用。 接收的消息会发送到所有连接到中心的客户端。

## <a name="test-the-app"></a>测试应用

确认应用遵循以下步骤。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在 release 模式下运行 Webpack。 使用“包管理器控制台”窗口，在项目根目录中运行以下命令。 如果不在项目根中，请在输入该命令之前输入 `cd SignalRWebPack`。

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. 选择“调试” > “开始执行(不调试)”，在不附加调试器的情况下在浏览器中启动应用 。 在 `http://localhost:<port_number>` 上提供 *wwwroot/index.html* 文件。

   如果遇到编译错误，请尝试关闭并重新打开解决方案。 

1. 打开另一个浏览器实例（任意浏览器）。 在地址栏中粘贴 URL。

1. 选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。 两个页面上立即显示唯一的用户名和消息。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 通过在项目根中执行以下命令以 release 模式运行 Webpack：

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. 通过在项目根中执行以下命令生成和运行应用：

    ```dotnetcli
    dotnet run
    ```

    Web 服务器启动应用并在 localhost 上提供。

1. 打开浏览器，转到 `http://localhost:<port_number>`。 提供 wwwroot/index.html 文件。 从地址栏复制 URL。

1. 打开另一个浏览器实例（任意浏览器）。 在地址栏中粘贴 URL。

1. 选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。 两个页面上立即显示唯一的用户名和消息。

---

![两个浏览器窗口都显示的消息](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载
* [.NET Core SDK 2.2 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* 带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [适用于 Visual Studio Code 的 C# 版本 1.17.1 或更高版本](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* 带 [npm](https://www.npmjs.com/) 的 [Node.js](https://nodejs.org/)

---

## <a name="create-the-aspnet-core-web-app"></a>创建 ASP.NET Core Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

配置 Visual Studio，在 PATH 环境变量中查找 npm。 默认情况下，Visual Studio 使用在安装目录中找到的 npm 版本。 在 Visual Studio 中按照以下说明执行操作：

1. 导航到“工具”>“选项”>“项目和解决方案”>“Web 包管理”>“外部 Web 工具”。
1. 在列表中选择 $(PATH) 项。 单击向上键将项移动列表第二个位置。

    ![Visual Studio 配置](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

已完成 Visual Studio 配置。 可以开始创建项目了。

1. 使用“文件”>“新建”>“项目”菜单选项，然后选择“ASP.NET Core Web 应用程序”模板   。
1. 将项目命名为 SignalRWebPack 并选择“创建”。
1. 从目标框架下拉列表选择 .NET Core 并从框架选择器下拉列表选择 ASP.NET Core 2.2 。 选择“空白”模板并选择“创建” 。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在“集成终端”中运行以下命令：

```dotnetcli
dotnet new web -o SignalRWebPack
```

SignalRWebPack 目录中创建了一个面向 .NET Core 的空 ASP.NET Core Web 应用。

---

## <a name="configure-webpack-and-typescript"></a>配置 Webpack 和 TypeScript

以下步骤配置 TypeScript 到 JavaScript 的转换和客户端资源的捆绑。

1. 在项目根目录中运行以下命令，创建 package.json 文件：

    ```console
    npm init -y
    ```

1. 将突出显示的属性添加到 package.json 文件：

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    将 `private` 属性设置为 `true`，防止下一步出现包安装警告。

1. 安装所需的 npm 包。 从项目根目录运行以下命令：

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    需要注意的一些命令细节：

    * 每个包名称中 `@` 符号后是版本号。 npm 安装这些特定的包版本。
    * `-E` 选项禁用 npm 将[语义化版本控制](https://semver.org/)范围运算符写到 package.json 的默认行为。 例如，使用 `"webpack": "4.29.3"` 而不是 `"webpack": "^4.29.3"`。 此选项防止意外升级到新的包版本。

    有关详细信息，请参阅 [npm-install](https://docs.npmjs.com/cli/install) 文档。

1. 将 package.json 文件的 `scripts` 属性替换为以下代码：

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    脚本的一些解释：

    * `build`：在开发模式下捆绑客户端资源并观察文件更改。 文件观察程序使捆绑在每次项目文件发生更改时重新生成。 `mode` 选项可禁用生产优化，例如摇树优化和缩小优化。 仅在开发中使用 `build`。
    * `release`：在生产模式下捆绑客户端资源。
    * `publish`：运行 `release` 脚本，在生产模式下捆绑客户端资源。 它调用 .NET Core CLI 的 [publish](/dotnet/core/tools/dotnet-publish) 命令发布应用。

1. 在项目根目录中创建名为 webpack.config.js 的文件，使其包含以下代码：

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    前面的文件配置 Webpack 编译。 需要注意的一些配置细节：

    * `output` 属性替代 dist 的默认值。 捆绑反而在 wwwroot 目录中发出。
    * `resolve.extensions` 数组包含 .js，以便导入 SignalR 客户端 JavaScript。

1. 在项目根目录中创建新的 src 目录，以存储项目的客户端资产。

1. 创建包含以下标记的 src/index.html。

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    前面的 HTML 定义主页的样板标记。

1. 创建新的 src/css 目录。 目的是存储项目的 .css 文件。

1. 创建包含以下标记的 src/css/main.css：

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    前面的 main.css 文件设计应用样式。

1. 创建包含以下 JSON 的 src/tsconfig.json：

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    前面的代码配置 TypeScript 编译器，生成与 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 兼容的 JavaScript。

1. 创建包含以下代码的 src/index.ts：

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    前面的 TypeScript 检索对 DOM 元素的引用并附加两个事件处理程序：

    * `keyup`：用户在 `tbMessage` 文本框中键入时触发此事件。 用户按 Enter 时调用 `send` 函数。
    * `click`：用户单击“发送”按钮时触发此事件。 调用 `send` 函数。

## <a name="configure-the-aspnet-core-app"></a>配置 ASP.NET Core 应用

1. `Startup.Configure` 方法中提供的代码显示 Hello World!。 将 `app.Run` 方法调用替换为对 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 和 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    前面的代码允许服务器定位并提供 index.html 文件，无论用户输入完整 URL 还是 Web 应用的根 URL。

1. 在 `Startup.ConfigureServices` 中，调用 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)。 此操作会将 SignalR 服务添加到项目。

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. 将 /hub 路由映射到 `ChatHub` 中心。 在 `Startup.Configure` 的末尾添加以下行：

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. 在项目根中创建名为 Hubs 的新目录。 目的是存储 SignalR 中心（在下一步中创建）。

1. 创建包含以下代码的中心 Hubs/ChatHub.cs：

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. 在 Startup.cs 文件顶部添加以下代码，解析 `ChatHub` 引用：

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>启用客户端和服务器通信

应用当前显示一个发送消息的简单窗体。 尝试执行此操作时没有任何反应。 服务器正在侦听特定的路由，但是不涉及发送消息。

1. 在项目根目录运行以下命令：

    ```console
    npm install @aspnet/signalr
    ```

    前面的命令将安装 [SignalR TypeScript 客户端](https://www.npmjs.com/package/@microsoft/signalr)，它允许客户端向服务器发送消息。

1. 将突出显示的代码添加到 src/index.ts 文件：

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    前面的代码支持从服务器接收消息。 `HubConnectionBuilder` 类创建新的生成器，用于配置服务器连接。 `withUrl` 函数配置中心 URL。

    SignalR 启用客户端和服务器之间的消息交换。 每个消息都有特定的名称。 例如，名为 `messageReceived` 的消息可以运行负责在消息区域显示新消息的逻辑。 可以通过 `on` 函数完成对特定消息的侦听。 可以侦听任意数量的消息名称。 还可以将参数传递到消息，例如所接收消息的作者姓名和内容。 客户端收到一条消息后，会创建一个新的 `div` 元素并在其 `innerHTML` 属性中显示作者姓名和消息内容。 新消息将添加到显示消息的主 `div` 元素中。

1. 客户端可以接收消息后，将它配置为发送消息。 将突出显示的代码添加到 src/index.ts 文件：

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    通过 WebSockets 连接发送消息需要调用 `send` 方法。 该方法的第一个参数是消息名称。 消息数据包含其他参数。 在此示例中，一条标识为 `newMessage` 的消息已发送到服务器。 该消息包含用户名和文本框中的用户输入。 如果发送成功，会清空文本框。

1. 将 `NewMessage` 方法添加到 `ChatHub` 类：

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    服务器收到消息后，前面的代码会将这些消息播发到所有连接的用户。 没有必要使用泛型 `on` 方法接收所有消息。 使用以消息名称命名的方法就可以了。

    在此示例中，TypeScript 客户端发送一条标识为 `newMessage` 的消息。 C# `NewMessage` 方法需要客户端发送的数据。 在 [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) 上对 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 进行调用。 接收的消息会发送到所有连接到中心的客户端。

## <a name="test-the-app"></a>测试应用

确认应用遵循以下步骤。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在 release 模式下运行 Webpack。 使用“包管理器控制台”窗口，在项目根目录中运行以下命令。 如果不在项目根中，请在输入该命令之前输入 `cd SignalRWebPack`。

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. 选择“调试” > “开始执行(不调试)”，在不附加调试器的情况下在浏览器中启动应用 。 在 `http://localhost:<port_number>` 上提供 *wwwroot/index.html* 文件。

1. 打开另一个浏览器实例（任意浏览器）。 在地址栏中粘贴 URL。

1. 选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。 两个页面上立即显示唯一的用户名和消息。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 通过在项目根中执行以下命令以 release 模式运行 Webpack：

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. 通过在项目根中执行以下命令生成和运行应用：

    ```dotnetcli
    dotnet run
    ```

    Web 服务器启动应用并在 localhost 上提供。

1. 打开浏览器，转到 `http://localhost:<port_number>`。 提供 wwwroot/index.html 文件。 从地址栏复制 URL。

1. 打开另一个浏览器实例（任意浏览器）。 在地址栏中粘贴 URL。

1. 选择一个浏览器，在“消息”文本框中键入任意内容，然后单击“发送”按钮 。 两个页面上立即显示唯一的用户名和消息。

---

![两个浏览器窗口都显示的消息](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
