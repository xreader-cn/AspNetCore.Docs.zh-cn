---
title: 托管和部署 Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: c8a65b08582102af9129cf71ac4a108a905e49fc
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085536"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="2ad13-103">托管和部署 Blazor</span><span class="sxs-lookup"><span data-stu-id="2ad13-103">Host and deploy Blazor</span></span>

<span data-ttu-id="2ad13-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2ad13-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="2ad13-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="2ad13-105">Publish the app</span></span>

<span data-ttu-id="2ad13-106">发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="2ad13-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2ad13-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ad13-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2ad13-108">从导航栏中选择“生成” > “发布{应用程序}”。</span><span class="sxs-lookup"><span data-stu-id="2ad13-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="2ad13-109">选择“发布目标”。</span><span class="sxs-lookup"><span data-stu-id="2ad13-109">Select the *publish target*.</span></span> <span data-ttu-id="2ad13-110">若要在本地发布，请选择“文件夹”。</span><span class="sxs-lookup"><span data-stu-id="2ad13-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="2ad13-111">接受“选择文件夹”字段中的默认位置，或指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="2ad13-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="2ad13-112">选择“发布”按钮。</span><span class="sxs-lookup"><span data-stu-id="2ad13-112">Select the **Publish** button.</span></span>


# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="2ad13-113">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2ad13-113">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="2ad13-114">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布具有发布配置的应用：</span><span class="sxs-lookup"><span data-stu-id="2ad13-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="2ad13-115">创建部署资产前，发布应用将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)生成该项目。</span><span class="sxs-lookup"><span data-stu-id="2ad13-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="2ad13-116">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="2ad13-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="2ad13-117">Blazor 客户端应用将发布到 /bin/Release/{TARGET FRAMEWORK}/dist 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2ad13-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/dist* folder.</span></span> <span data-ttu-id="2ad13-118">Blazor 服务器端应用将发布到 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2ad13-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="2ad13-119">文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="2ad13-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="2ad13-120">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="2ad13-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="2ad13-121">部署</span><span class="sxs-lookup"><span data-stu-id="2ad13-121">Deployment</span></span>

<span data-ttu-id="2ad13-122">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="2ad13-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
