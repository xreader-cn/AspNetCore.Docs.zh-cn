---
title: 使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure
author: rick-anderson
description: 了解如何使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure 应用服务
ms.author: riserrad
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: 5f117cb2867a6e7b54269ef39abe819256b429ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242673"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>使用 Visual Studio Code 将 ASP.NET Core 应用发布到 Azure

作者：[Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

若要对应用服务部署问题进行故障排除，请参阅 <xref:test/troubleshoot-azure-iis>。

## <a name="intro"></a>简介

借助此教程，可了解如何创建 ASP.Net Core MVC 应用程序并在 Visual Studio Code 中部署。

## <a name="set-up"></a>设置

- 如果没有[免费 Azure 帐户](https://azure.microsoft.com/free/dotnet/)，请开设一个。
- 安装 [.NET Core SDK](https://dotnet.microsoft.com/download)
- 安装 [Visual Studio Code](https://code.visualstudio.com/Download)
  - 将 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)安装到 Visual Studio Code
  - 将 [Azure 应用服务扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)安装到 Visual Studio Code 并在继续操作之前对其进行配置

## <a name="create-an-aspnet-core-mvc-project"></a>创建 ASP.NET Core MVC 项目

使用终端导航到要在其中创建项目的文件夹，然后使用以下命令：

```dotnetcli
dotnet new mvc
```

将生成类似于以下的文件夹结构：

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>使用 Visual Studio Code 打开

创建项目后，可使用以下选项之一通过 Visual Studio Code 将其打开：

### <a name="through-the-command-line"></a>通过命令行

在创建项目的文件夹中使用以下命令：

```cmd
> code .
```

如果以下命令不起作用，请参考[此链接](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform)，检查安装是否配置正确。

### <a name="through-visual-studio-code-interface"></a>通过 Visual Studio Code 接口

- 打开 Visual Studio Code
- 在菜单上，选择 `File > Open Folder`
- 选择创建 MVC 项目的文件夹的根目录

打开项目文件夹后，会收到消息，告知生成和调试所需的资产丢失。 接受帮助以添加资产。

![加载了项目的 Visual Studio Code 接口](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

将在项目结构下创建 `.vscode` 文件夹。 它将包含以下文件：

```cmd
launch.json
tasks.json
```

这些实用程序软件可帮助生成和调试 .NET Core Web 应用。

## <a name="run-the-app"></a>运行应用

将应用部署到 Azure 之前，请确保它在本地计算机上正常运行。

- 按 F5 运行项目

Web 应用将在默认浏览器的新选项卡上开始运行。 它刚启动时可能会显示一个隐私问题警告。 这是因为将使用 HTTP 或 HTTPS 启动应用，会默认导航到 HTTPS 终结点。

![本地调试应用时显示的隐私问题警告](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

若要保持调试会话，请单击 `Advanced` 然后单击 `Continue to localhost (unsafe)`。

## <a name="generate-the-deployment-package-locally"></a>本地生成部署包

- 打开 Visual Studio Code 终端
- 使用以下命令将 `Release` 包生成到名为 `publish` 的子文件夹中：
  - `dotnet publish -c Release -o ./publish`
- 将在项目结构下创建新的 `publish` 文件夹

![发布文件夹结构](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>发布到 Azure 应用服务

利用适用于 Visual Studio Code 的 Azure 应用服务扩展，按照以下步骤直接将网站发布到 Azure 应用服务。

### <a name="if-youre-creating-a-new-web-app"></a>如果要创建新的 Web 应用

- 请右键单击 `publish` 文件夹，然后选择 `Deploy to Web App...`
- 选择要创建 Web 应用的订阅
- 选择 `Create New Web App`
- 输入 Web 应用的名称

此扩展将创建新的 Web 应用，然后自动开始将包部署到该 Web 应用。 完成部署后，请单击 `Browse Website` 以验证部署。

![部署成功的消息](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

单击 `Browse Website` 后，将使用默认浏览器导航到此网站：

![已成功部署新的 Web 应用](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>如果要部署到现有 Web 应用

- 请右键单击 `publish` 文件夹，然后选择 `Deploy to Web App...`
- 选择现有 Web 应用所在的订阅
- 从列表中选择 Web 应用
- Visual Studio Code 将询问是否要覆盖现有内容。 单击 `Deploy` 以确认

该扩展将更新的内容部署到 Web 应用。 完成后，单击 `Browse Website` 以验证部署。

![已成功部署现有 Web 应用](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>后续步骤

- [创建首个 Azure DevOps 管道](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>其他资源

- [Azure 应用服务](/azure/app-service/app-service-web-overview)
- [Azure 资源组](/azure/azure-resource-manager/resource-group-overview#resource-groups)
