---
title: 用于 ASP.NET Core Blazor 的工具
author: guardrex
description: 了解可用于构建 Blazor 应用程序的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536241"
---
# <a name="tooling-for-aspnet-core-blazor"></a>用于 ASP.NET Core Blazor 的工具

::: zone pivot="windows"

1. 安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。

1. 创建新项目。

1. 选择“Blazor 应用”。 选择“下一步”。

1. 在“项目名称”字段提供项目名称，或接受默认项目名称。 确认“位置”条目正确无误或为项目提供位置。 选择“创建”。

1. 若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。 若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。 选择“创建”。

   对于托管的 Blazor WebAssembly 体验，选择“托管的 ASP.NET Core”复选框。

   若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。

1. 按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。

有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。

执行托管应用 Blazor WebAssembly 时，请从解决方案的 `Server` 项目运行应用。

::: zone-end

::: zone pivot="linux"

1. 安装最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。 如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：

   ```dotnetcli
   dotnet --version
   ```

1. 安装最新版本的 [Visual Studio Code](https://code.visualstudio.com)。

1. 安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

1. 若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   对于托管的 Blazor WebAssembly 体验，请向命令添加托管选项 (`-ho` 或 `--hosted`) 选项：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。

1. 在 Visual Studio Code 中打开 `WebApplication1` 文件夹，

1. IDE 要求添加资产以用于生成和调试项目。 选择 **“是”** 。

   **托管的 Blazor WebAssembly 启动和任务配置**

   对于托管的 Blazor WebAssembly 解决方案，请将包含 `launch.json` 和 `tasks.json` 文件的 `.vscode` 文件夹添加（或移动）到解决方案的父文件夹中，父文件夹包含常见的项目文件夹名称 `Client`、`Server` 和 `Shared`。 更新或确认 `launch.json` 和 `tasks.json` 文件中的配置是否从 `Server` 项目执行托管应用 Blazor WebAssembly。

   **`.vscode/launch.json`** （`launch` 配置）：

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   在当前工作目录 (`cwd`) 的前面配置中，`{SERVER APP FOLDER}` 占位符是 `Server` 项目的文件夹，通常为“`Server`”。

   如果使用 Microsoft Edge 并且系统上未安装 Google Chrome，请在配置中添加额外属性 `"browser": "edge"`。

   `Server` 项目文件夹的示例，该示例将生成 Microsoft Edge（而不是默认浏览器 Google Chrome）作为调试运行的浏览器：

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`** （[`dotnet` 命令](/dotnet/core/tools/dotnet)参数）：

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   在上述参数中：

   * `{SERVER APP FOLDER}` 占位符是 `Server` 项目的文件夹，通常为“`Server`”。
   * `{PROJECT NAME}` 占位符是应用的名称，通常基于从 Blazor 项目模板生成的应用中后跟“`.Server`”的解决方案的名称。

   来自[将 SignalR 与 Blazor WebAssembly 应用配合使用的教程](xref:tutorials/signalr-blazor)的以下示例使用项目文件夹名称 `Server` 和项目名称 `BlazorWebAssemblySignalRApp.Server`：

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. 按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。

## <a name="trust-a-development-certificate"></a>信任开发证书

Linux 上没有用于信任证书的集中途径。 通常采用以下方法之一：

* 在浏览器的排除列表中排除应用的 URL。
* 信任 `localhost` 的所有自签名证书。
* 将证书添加到浏览器的受信任证书列表。

有关详细信息，请参阅浏览器制造商和 Linux 发行版提供的指南。

::: zone-end

::: zone pivot="macos"

1. 安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。

1. 选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。

1. 在边栏中，选择“Web 和控制台” > “应用”。 

   若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。 若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。 选择“下一步”。

   若要了解 Blazor WebAssembly（独立和托管）和 Blazor Server这两个 Blazor 托管模型，请参阅 <xref:blazor/hosting-models> 。

1. 确认已将“身份验证”设置为“无身份验证”。 选择“下一步”。

1. 对于托管的 Blazor WebAssembly 体验，选择“托管的 ASP.NET Core”复选框。

1. 在“项目名称”字段中，将应用命名为 `WebApplication1`。 选择“创建”。

1. 选择“运行” > “启动而不调试”以不使用调试程序运行应用 。 使用“运行” > “开始调试”运行应用，或者使用“运行 (&#9654;)”按钮通过调试程序运行应用。 

如果出现信任开发证书的提示，请信任证书并继续操作。 信任证书需要使用用户密码和密钥链密码。 有关信任 ASP.NET Core HTTPS 开发证书的详细信息，请参阅 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>。

执行托管应用 Blazor WebAssembly 时，请从解决方案的 `Server` 项目运行应用。

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>使用适用于跨平台 Blazor 开发的 Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) 是一个开源的跨平台集成开发环境 (IDE)，可用于开发 Blazor 应用。 使用 .NET CLI 创建新的 Blazor 应用，来使用 Visual Studio Code 进行开发。 有关详细信息，请选择[本文的 Linux 版本](?pivots=linux)。

## <a name="blazor-template-options"></a>Blazor 模板选项

Blazor 框架提供了一些模板，用于为每个 Blazor 托管模型（共两个）创建新应用。 模板用于创建新的 Blazor 项目和解决方案，而不考虑您选择用于 Blazor 开发的工具（Visual Studio、Visual Studio for Mac、Visual Studio Code 还是 .NET CLI）：

* Blazor WebAssembly 项目模板：`blazorwasm`
* Blazor Server项目模板：`blazorserver`

有关 Blazor 的托管模型的详细信息，请参阅 <xref:blazor/hosting-models>。

通过将 help 选项（`-h` 或 `--help`）传递给命令行界面中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI 命令，可使用模板选项：

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
