---
title: 工具和下载 - 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 通过 ASP.NET Core 和 Azure 实现 DevOps 所需的工具和下载。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511140"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="dd073-103">工具和下载</span><span class="sxs-lookup"><span data-stu-id="dd073-103">Tools and downloads</span></span>

<span data-ttu-id="dd073-104">Azure 拥有多个用于预配和管理资源的界面，例如 [Azure 门户](https://portal.azure.com)、[Azure CLI](/cli/azure/)、[Azure PowerShell](/powershell/azure/overview)、[Azure Cloud Shell](https://shell.azure.com/bash) 和 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="dd073-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="dd073-105">本指南采用最简单的方法，并在可能的情况下使用 Azure Cloud Shell 减少所需执行的步骤。</span><span class="sxs-lookup"><span data-stu-id="dd073-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="dd073-106">但是，某些部分必须使用 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="dd073-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dd073-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="dd073-107">Prerequisites</span></span>

<span data-ttu-id="dd073-108">需要以下订阅：</span><span class="sxs-lookup"><span data-stu-id="dd073-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="dd073-109">Azure &mdash; 如果没有帐户，请[获取免费试用版](https://azure.microsoft.com/free/)。</span><span class="sxs-lookup"><span data-stu-id="dd073-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="dd073-110">Azure DevOps Services &mdash; 在第 4 章中创建 Azure DevOps 订阅和组织。</span><span class="sxs-lookup"><span data-stu-id="dd073-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="dd073-111">GitHub &mdash; 如果没有帐户，请[免费注册](https://github.com/join)。</span><span class="sxs-lookup"><span data-stu-id="dd073-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="dd073-112">需要以下工具：</span><span class="sxs-lookup"><span data-stu-id="dd073-112">The following tools are required:</span></span>

* <span data-ttu-id="dd073-113">[Git](https://git-scm.com/downloads) &mdash; 本指南建议对 Git 有基本的了解。</span><span class="sxs-lookup"><span data-stu-id="dd073-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="dd073-114">查看 [Git 文档](https://git-scm.com/doc)，特别是 [git remote](https://git-scm.com/docs/git-remote) 和 [git push](https://git-scm.com/docs/git-push)。</span><span class="sxs-lookup"><span data-stu-id="dd073-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="dd073-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; 必须使用版本 2.1.300 或更高版本才能生成和运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="dd073-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="dd073-116">如果随 **.NET Core 跨平台开发**工作负荷安装了 Visual Studio，则 .NET Core SDK 已安装。</span><span class="sxs-lookup"><span data-stu-id="dd073-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="dd073-117">验证 .NET Core SDK 安装。</span><span class="sxs-lookup"><span data-stu-id="dd073-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="dd073-118">打开命令行界面，然后运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="dd073-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="dd073-119">建议工具（仅限 Windows）</span><span class="sxs-lookup"><span data-stu-id="dd073-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="dd073-120">[Visual Studio](https://visualstudio.microsoft.com) 功能强大的 Azure 工具提供适用于本指南所述大部分功能的 GUI。</span><span class="sxs-lookup"><span data-stu-id="dd073-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="dd073-121">任何版本的 Visual Studio 都可以正常运行，包括免费的 Visual Studio Community Edition。</span><span class="sxs-lookup"><span data-stu-id="dd073-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="dd073-122">编写教程旨在演示如何使用和不使用 Visual Studio 实现开发、部署和 DevOps。</span><span class="sxs-lookup"><span data-stu-id="dd073-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="dd073-123">确认 Visual Studio 已安装以下[工作负荷](/visualstudio/install/modify-visual-studio)：</span><span class="sxs-lookup"><span data-stu-id="dd073-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="dd073-124">ASP.NET 和 Web 开发</span><span class="sxs-lookup"><span data-stu-id="dd073-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="dd073-125">Azure 开发</span><span class="sxs-lookup"><span data-stu-id="dd073-125">Azure development</span></span>
  * <span data-ttu-id="dd073-126">.NET Core 跨平台开发</span><span class="sxs-lookup"><span data-stu-id="dd073-126">.NET Core cross-platform development</span></span>
