---
title: 托管和部署 Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 774fbb6fdaab14a015db4fb39de2e1ea73a1837b
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982635"
---
# <a name="host-and-deploy-blazor"></a>托管和部署 Blazor

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>发布应用

使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用，用于发布配置中的部署。 集成式开发环境 (IDE) 可以使用内置发布功能自动执行 `dotnet publish` 命令，因此可能无需从命令提示符手动执行命令，具体取决于使用的开发工具。

```console
dotnet publish -c Release
```

创建部署资产前，`dotnet publish` 将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)该项目。 在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。 部署在 */bin/Release/{TARGET FRAMEWORK}/publish* 文件夹中创建。

“publish”文件夹中的资产将部署到 Web 服务器。 部署可能是手动或自动化过程，具体取决于使用的开发工具。

## <a name="deployment"></a>部署

有关部署指南，请参阅以下主题：

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
