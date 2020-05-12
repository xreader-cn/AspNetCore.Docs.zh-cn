---
title: 结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly
author: guardrex
description: 创建结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly 的聊天应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773783"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

本教程介绍使用 SignalR 和 Blazor WebAssembly 生成实时应用的基础知识。 您将学习如何：

> [!div class="checklist"]
> * 创建 Blazor WebAssembly 托管应用项目
> * 添加 SignalR 客户端库
> * 添加 SignalR 集线器
> * 添加 SignalR 服务和 SignalR 集线器的终结点
> * 添加用于聊天的 Razor 组件代码

在本教程结束时，你将拥有一个正常运行的聊天应用。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>创建托管 Blazor WebAssembly 应用项目

若未使用 Visual Studio 版本 16.6 预览版 2 或更高版本，请安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 模板。 当 Blazor WebAssembly 处于预览状态时，[ Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) 包具有预览版本。 在命令行界面中执行以下命令：

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

按照所选工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建新项目。

1. 选择“Blazor 应用”  ，然后选择“下一步”  。

1. 在“项目名称”  字段中键入“BlazorSignalRApp”。 确认“位置”  条目正确无误或为项目提供位置。 选择“创建”  。

1. 选择“Blazor WebAssembly 应用”  模板。

1. 在“高级”  下选中“托管的 ASP.NET Core”  复选框。

1. 选择“创建”  。

> [!NOTE]
> 如果升级或安装了新版 Visual Studio，并且 Blazor WebAssembly 模板没有出现在 VS UI 中，请使用前面显示的 `dotnet new` 命令重新安装该模板。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. 在 Visual Studio Code 中打开应用的项目文件夹。

1. 当显示添加资产以生成和调试应用的对话框时，选择“是”  。 Visual Studio Code 会自动添加“.vscode”  文件夹以及生成的“launch.json”  和“tasks.json”  文件。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. 在 Visual Studio for Mac 中，通过导航到项目文件夹并打开项目的解决方案文件 (.sln  ) 打开项目。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>添加 SignalR 客户端库

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在“解决方案资源管理器”中，右键单击“BlazorSignalRApp.Client”项目，然后选择“管理 NuGet 包”    。

1. 在“管理 NuGet 包”  对话框中，确认“包源”  设置为“nuget.org”  。

1. 选择“浏览”  后，在搜索框中键入“Microsoft.AspNetCore.SignalR.Client”。

1. 在搜索结果中，选中 `Microsoft.AspNetCore.SignalR.Client` 包，然后选择“安装”  。

1. 如果出现“预览更改”  对话框，则选择“确定”  。

1. 如果出现“许可证接受”  对话框，如果你同意许可条款，请选择“我接受”  。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在“集成终端”  （工具栏上的“视图”   > “终端”  ）中，执行以下命令：

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案”  边栏中，右键单击“BlazorSignalRApp.Client”  项目，然后选择“管理 NuGet 包”  。

1. 在“管理 NuGet 包”  对话框中，确认源下拉列表设置为“nuget.org”  。

1. 选择“浏览”  后，在搜索框中键入“Microsoft.AspNetCore.SignalR.Client”。

1. 在搜索结果中，选中 `Microsoft.AspNetCore.SignalR.Client` 包旁边的复选框，然后选择“添加包”  。

1. 出现“许可证接受”  对话框时，如果你同意许可条款，请选择“接受”  。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在命令行界面中执行以下命令：

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>添加 SignalR 集线器

在“BlazorSignalRApp.Server”  项目中，创建一个“Hubs”  （复数）文件夹，并添加以下 `ChatHub` 类 (Hubs/ChatHub.cs  )：

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>添加服务和 SignalR 集线器的终结点

1. 在“BlazorSignalRApp.Server”  项目中，打开“Startup.cs”  文件。

1. 将 `ChatHub` 类的命名空间添加到文件顶部：

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. 将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. 在控制器终结点和客户端回退之间的 `Startup.Configure` 中，为集线器添加一个终结点：

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>添加用于聊天的 Razor 组件代码

1. 在“BlazorSignalRApp.Client”  项目中，打开“Pages/Index.razor”  文件。

1. 将标记替换为以下代码：

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>运行应用

1. 按照工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在“解决方案资源管理器”  中，选择“BlazorSignalRApp.Server”  项目。 按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。 两个页面上立即显示名称和消息：

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 当 VS Code 主动为服务器应用创建一个启动配置文件 (.vscode/launch.json) 时，`program` 条目如下所示，它指向应用的程序集 (`{APPLICATION NAME}.Server.dll`)  ：

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. 按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。 两个页面上立即显示名称和消息：

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案”  边栏中，选择“BlazorSignalRApp.Server”  项目。 按 <kbd>⌘</kbd>+<kbd>↩</kbd>** 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。 两个页面上立即显示名称和消息：

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 在命令行界面中执行以下命令：

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。 两个页面上立即显示名称和消息：

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Blazor WebAssembly 托管应用项目
> * 添加 SignalR 客户端库
> * 添加 SignalR 集线器
> * 添加 SignalR 服务和 SignalR 中心的终结点
> * 添加用于聊天的 Razor 组件代码

若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>其他资源

* <xref:signalr/introduction>
