---
title: 托管和部署 Razor 组件和 Blazor
author: guardrex
description: 了解如何托管和部署 Razor 组件和 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/razor-components-blazor/index
ms.openlocfilehash: eeaa27bef584523b77d8b47000b310fe0cac7341
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59070304"
---
# <a name="host-and-deploy-razor-components-and-blazor"></a><span data-ttu-id="36728-103">托管和部署 Razor 组件和 Blazor</span><span class="sxs-lookup"><span data-stu-id="36728-103">Host and deploy Razor Components and Blazor</span></span>

<span data-ttu-id="36728-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="36728-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="36728-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="36728-105">Publish the app</span></span>

<span data-ttu-id="36728-106">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="36728-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="36728-107">集成式开发环境 (IDE) 可以使用内置发布功能自动执行 `dotnet publish` 命令，因此可能无需从命令提示符手动执行命令，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="36728-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

`dotnet publish` <span data-ttu-id="36728-108">在创建部署资产之前触发项目依赖关系还原 ([restore](/dotnet/core/tools/dotnet-restore)) 和项目生成 ([builds](/dotnet/core/tools/dotnet-build))。</span><span class="sxs-lookup"><span data-stu-id="36728-108">triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="36728-109">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="36728-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="36728-110">部署在 */bin/Release/{TARGET FRAMEWORK}/publish* 文件夹中创建。</span><span class="sxs-lookup"><span data-stu-id="36728-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="36728-111">“publish”文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="36728-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="36728-112">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="36728-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="36728-113">部署</span><span class="sxs-lookup"><span data-stu-id="36728-113">Deployment</span></span>

<span data-ttu-id="36728-114">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="36728-114">For deployment guidance, see the following topics:</span></span>

* [<span data-ttu-id="36728-115">客户端 Blazor</span><span class="sxs-lookup"><span data-stu-id="36728-115">Client-side Blazor</span></span>](xref:host-and-deploy/razor-components-blazor/blazor)
* [<span data-ttu-id="36728-116">服务器端 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="36728-116">Server-side ASP.NET Core Razor Components</span></span>](xref:host-and-deploy/razor-components-blazor/razor-components)
