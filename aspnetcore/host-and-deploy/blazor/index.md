---
title: 托管和部署 Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 0fc7643c65b93a63d7a594d35e4013eab76e9db8
ms.sourcegitcommit: 4d05e30567279072f1b070618afe58ae1bcefd5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376383"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="e1923-103">托管和部署 Blazor</span><span class="sxs-lookup"><span data-stu-id="e1923-103">Host and deploy Blazor</span></span>

<span data-ttu-id="e1923-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e1923-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="e1923-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="e1923-105">Publish the app</span></span>

<span data-ttu-id="e1923-106">发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="e1923-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e1923-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e1923-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="e1923-108">从导航栏中选择“生成”   > “发布{应用程序}”  。</span><span class="sxs-lookup"><span data-stu-id="e1923-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="e1923-109">选择“发布目标”  。</span><span class="sxs-lookup"><span data-stu-id="e1923-109">Select the *publish target*.</span></span> <span data-ttu-id="e1923-110">若要在本地发布，请选择“文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="e1923-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="e1923-111">接受“选择文件夹”  字段中的默认位置，或指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="e1923-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="e1923-112">选择“发布”  按钮。</span><span class="sxs-lookup"><span data-stu-id="e1923-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="e1923-113">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e1923-113">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="e1923-114">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布具有发布配置的应用：</span><span class="sxs-lookup"><span data-stu-id="e1923-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="e1923-115">创建部署资产前，发布应用将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)生成该项目。</span><span class="sxs-lookup"><span data-stu-id="e1923-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="e1923-116">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="e1923-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="e1923-117">Blazor 客户端应用发布到 /bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="e1923-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="e1923-118">Blazor 服务器端应用将发布到 /bin/Release/{TARGET FRAMEWORK}/publish  文件夹。</span><span class="sxs-lookup"><span data-stu-id="e1923-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="e1923-119">文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="e1923-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="e1923-120">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="e1923-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="e1923-121">部署</span><span class="sxs-lookup"><span data-stu-id="e1923-121">Deployment</span></span>

<span data-ttu-id="e1923-122">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="e1923-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="e1923-123">使用 Azure 存储实现 Blazor 无服务器托管</span><span class="sxs-lookup"><span data-stu-id="e1923-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="e1923-124">可以使用 [Azure 存储](https://azure.microsoft.com/services/storage/)将 Blazor 客户端应用作为直接来自存储容器的静态内容提供。</span><span class="sxs-lookup"><span data-stu-id="e1923-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="e1923-125">有关详细信息，请参阅[托管和部署 Blazor 客户端（独立部署）：Azure 存储](xref:host-and-deploy/blazor/client-side#azure-storage)。</span><span class="sxs-lookup"><span data-stu-id="e1923-125">For more information, see [Host and deploy Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
