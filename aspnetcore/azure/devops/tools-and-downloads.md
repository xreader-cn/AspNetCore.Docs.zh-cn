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
# <a name="tools-and-downloads"></a><span data-ttu-id="029f1-103">工具和下载</span><span class="sxs-lookup"><span data-stu-id="029f1-103">Tools and downloads</span></span>

<span data-ttu-id="029f1-104">Azure 具有多个用于配置和管理资源的界面，例如 [Azure 门户](https://portal.azure.com)、[Azure CLI](/cli/azure/)、[Azure PowerShell](/powershell/azure/overview)、[ Azure Cloud Shell](https://shell.azure.com/bash)和 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="029f1-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="029f1-105">本指南采用极简主义方法，并尽可能使用 Azure Cloud Shell 来减少所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="029f1-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="029f1-106">但是，对于某些部分必须使用 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="029f1-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="029f1-107">系统必备</span><span class="sxs-lookup"><span data-stu-id="029f1-107">Prerequisites</span></span>

<span data-ttu-id="029f1-108">以下订阅是必需的：</span><span class="sxs-lookup"><span data-stu-id="029f1-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="029f1-109">Azure&mdash;如果还没有帐户，[获取免费试用版](https://azure.microsoft.com/free/)。</span><span class="sxs-lookup"><span data-stu-id="029f1-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="029f1-110">Azure DevOps 服务 &mdash; Azure DevOps 订阅和组织将在第 4 章中创建。</span><span class="sxs-lookup"><span data-stu-id="029f1-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="029f1-111">GitHub&mdash;如果还没有帐户，[免费注册](https://github.com/join)。</span><span class="sxs-lookup"><span data-stu-id="029f1-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="029f1-112">以下工具是必需的：</span><span class="sxs-lookup"><span data-stu-id="029f1-112">The following tools are required:</span></span>

* <span data-ttu-id="029f1-113">[Git](https://git-scm.com/downloads) &mdash; 本指南建议应对 Git 有基本的了解。</span><span class="sxs-lookup"><span data-stu-id="029f1-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="029f1-114">请查看 [Git 文档](https://git-scm.com/doc)，特别是[git remote](https://git-scm.com/docs/git-remote) 和 [git push](https://git-scm.com/docs/git-push)。</span><span class="sxs-lookup"><span data-stu-id="029f1-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="029f1-115">[.NET core SDK](https://www.microsoft.com/net/download/) &mdash; 2.1.300 或更高版本是构建和运行示例应用的必需条件。</span><span class="sxs-lookup"><span data-stu-id="029f1-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="029f1-116">如果 Visual Studio 安装了“.NET Core 跨平台开发”工作负荷，则表示已安装了 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="029f1-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="029f1-117">验证 .NET Core SDK 安装。</span><span class="sxs-lookup"><span data-stu-id="029f1-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="029f1-118">打开命令外壳，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="029f1-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="029f1-119">建议的工具 (仅 Windows)</span><span class="sxs-lookup"><span data-stu-id="029f1-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="029f1-120">[Visual Studio](https://visualstudio.microsoft.com) 可靠的 Azure 工具为本指南中描述的大多数功能提供了GUI。</span><span class="sxs-lookup"><span data-stu-id="029f1-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="029f1-121">任何版本的 Visual Studio 都可以使用，包括免费的 Visual Studio Community Edition。</span><span class="sxs-lookup"><span data-stu-id="029f1-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="029f1-122">编写本教程是为了演示在使用和不使用 Visual Studio 的情况下进行开发，部署和 DevOps。</span><span class="sxs-lookup"><span data-stu-id="029f1-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="029f1-123">确认 Visual Studio 具有以下[工作负荷](/visualstudio/install/modify-visual-studio)安装：</span><span class="sxs-lookup"><span data-stu-id="029f1-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="029f1-124">ASP.NET 和 Web 开发</span><span class="sxs-lookup"><span data-stu-id="029f1-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="029f1-125">Azure 开发</span><span class="sxs-lookup"><span data-stu-id="029f1-125">Azure development</span></span>
  * <span data-ttu-id="029f1-126">.NET Core 跨平台开发</span><span class="sxs-lookup"><span data-stu-id="029f1-126">.NET Core cross-platform development</span></span>
