---
title: 工具和下载-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 工具和下载所需的 ASP.NET Core 和 Azure 中的 DevOps。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080638"
---
# <a name="tools-and-downloads"></a>工具和下载

Azure 具有多个用于配置和管理资源的界面，例如 [Azure 门户](https://portal.azure.com)、[Azure CLI](/cli/azure/)、[Azure PowerShell](/powershell/azure/overview)、[ Azure Cloud Shell](https://shell.azure.com/bash)和 Visual Studio。 本指南采用极简主义方法，并尽可能使用 Azure Cloud Shell 来减少所需的步骤。 但是，对于某些部分必须使用 Azure 门户。

## <a name="prerequisites"></a>系统必备

以下订阅是必需的：

* Azure&mdash;如果还没有帐户，[获取免费试用版](https://azure.microsoft.com/free/)。
* Azure DevOps 服务 &mdash; Azure DevOps 订阅和组织将在第 4 章中创建。
* GitHub&mdash;如果还没有帐户，[免费注册](https://github.com/join)。

以下工具是必需的：

* [Git](https://git-scm.com/downloads) &mdash; 本指南建议应对 Git 有基本的了解。 请查看 [Git 文档](https://git-scm.com/doc)，特别是[git remote](https://git-scm.com/docs/git-remote) 和 [git push](https://git-scm.com/docs/git-push)。
* [.NET core SDK](https://www.microsoft.com/net/download/) &mdash; 2.1.300 或更高版本是构建和运行示例应用的必需条件。 如果 Visual Studio 安装了“.NET Core 跨平台开发”工作负荷，则表示已安装了 .NET Core SDK。

    验证 .NET Core SDK 安装。 打开命令外壳，并运行以下命令：

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>建议的工具 (仅 Windows)

* [Visual Studio](https://visualstudio.microsoft.com) 可靠的 Azure 工具为本指南中描述的大多数功能提供了GUI。 任何版本的 Visual Studio 都可以使用，包括免费的 Visual Studio Community Edition。 编写本教程是为了演示在使用和不使用 Visual Studio 的情况下进行开发，部署和 DevOps。

  确认 Visual Studio 具有以下[工作负荷](/visualstudio/install/modify-visual-studio)安装：

  * ASP.NET 和 Web 开发
  * Azure 开发
  * .NET Core 跨平台开发
