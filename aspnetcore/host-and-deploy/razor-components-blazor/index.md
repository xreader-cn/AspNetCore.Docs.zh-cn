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
# <a name="host-and-deploy-razor-components-and-blazor"></a>托管和部署 Razor 组件和 Blazor

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>发布应用

使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用，用于发布配置中的部署。 集成式开发环境 (IDE) 可以使用内置发布功能自动执行 `dotnet publish` 命令，因此可能无需从命令提示符手动执行命令，具体取决于使用的开发工具。

```console
dotnet publish -c Release
```

`dotnet publish` 在创建部署资产之前触发项目依赖关系还原 ([restore](/dotnet/core/tools/dotnet-restore)) 和项目生成 ([builds](/dotnet/core/tools/dotnet-build))。 在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。 部署在 */bin/Release/{TARGET FRAMEWORK}/publish* 文件夹中创建。

“publish”文件夹中的资产将部署到 Web 服务器。 部署可能是手动或自动化过程，具体取决于使用的开发工具。

## <a name="deployment"></a>部署

有关部署指南，请参阅以下主题：

* [客户端 Blazor](xref:host-and-deploy/razor-components-blazor/blazor)
* [服务器端 ASP.NET Core Razor 组件](xref:host-and-deploy/razor-components-blazor/razor-components)
