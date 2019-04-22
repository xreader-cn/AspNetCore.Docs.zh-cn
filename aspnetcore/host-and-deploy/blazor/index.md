---
title: 托管和部署 Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: a7739e2b240d7fd6c85e68105892c802228ebeb5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614557"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="b7b2f-103">托管和部署 Blazor</span><span class="sxs-lookup"><span data-stu-id="b7b2f-103">Host and deploy Blazor</span></span>

<span data-ttu-id="b7b2f-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b7b2f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="b7b2f-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="b7b2f-105">Publish the app</span></span>

<span data-ttu-id="b7b2f-106">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b7b2f-107">集成式开发环境 (IDE) 可以使用内置发布功能自动执行 `dotnet publish` 命令，因此可能无需从命令提示符手动执行命令，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="b7b2f-108">创建部署资产前，`dotnet publish` 将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)该项目。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b7b2f-109">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="b7b2f-110">部署在 */bin/Release/{TARGET FRAMEWORK}/publish* 文件夹中创建。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="b7b2f-111">“publish”文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="b7b2f-112">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="b7b2f-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="b7b2f-113">部署</span><span class="sxs-lookup"><span data-stu-id="b7b2f-113">Deployment</span></span>

<span data-ttu-id="b7b2f-114">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="b7b2f-114">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
