---
title: 工具和下载 - 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 通过 ASP.NET Core 和 Azure 实现 DevOps 所需的工具和下载。
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 3242031f07f3fa344422db4591ea1a244b6723dd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625240"
---
# <a name="tools-and-downloads"></a>工具和下载

Azure 拥有多个用于预配和管理资源的界面，例如 [Azure 门户](https://portal.azure.com)、[Azure CLI](/cli/azure/)、[Azure PowerShell](/powershell/azure/overview)、[Azure Cloud Shell](https://shell.azure.com/bash) 和 Visual Studio。 本指南采用最简单的方法，并在可能的情况下使用 Azure Cloud Shell 减少所需执行的步骤。 但是，某些部分必须使用 Azure 门户。

## <a name="prerequisites"></a>系统必备

需要以下订阅：

* Azure &mdash; 如果没有帐户，请[获取免费试用版](https://azure.microsoft.com/free/dotnet/)。
* Azure DevOps Services &mdash; 在第 4 章中创建 Azure DevOps 订阅和组织。
* GitHub &mdash; 如果没有帐户，请[免费注册](https://github.com/join)。

需要以下工具：

* [Git](https://git-scm.com/downloads) &mdash; 本指南建议对 Git 有基本的了解。 查看 [Git 文档](https://git-scm.com/doc)，特别是 [git remote](https://git-scm.com/docs/git-remote) 和 [git push](https://git-scm.com/docs/git-push)。
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; 必须使用版本 2.1.300 或更高版本才能生成和运行示例应用。 如果随 **.NET Core 跨平台开发**工作负荷安装了 Visual Studio，则 .NET Core SDK 已安装。

    验证 .NET Core SDK 安装。 打开命令行界面，然后运行以下命令：

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>建议工具（仅限 Windows）

* [Visual Studio](https://visualstudio.microsoft.com) 功能强大的 Azure 工具提供适用于本指南所述大部分功能的 GUI。 任何版本的 Visual Studio 都可以正常运行，包括免费的 Visual Studio Community Edition。 编写教程旨在演示如何使用和不使用 Visual Studio 实现开发、部署和 DevOps。

  确认 Visual Studio 已安装以下[工作负荷](/visualstudio/install/modify-visual-studio)：

  * ASP.NET 和 Web 开发
  * Azure 开发
  * .NET Core 跨平台开发
