---
title: 托管和部署 Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5def0356d13975211dd234f6a6a9f5a993d003b7
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223184"
---
# <a name="host-and-deploy-blazor"></a>托管和部署 Blazor

作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>发布应用

发布应用，用于发布配置中的部署。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 从导航栏中选择“生成”   > “发布{应用程序}”  。
1. 选择“发布目标”  。 若要在本地发布，请选择“文件夹”  。
1. 接受“选择文件夹”  字段中的默认位置，或指定其他位置。 选择“发布”  按钮。


# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布具有发布配置的应用：

```console
dotnet publish -c Release
```

---

创建部署资产前，发布应用将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)生成该项目。 在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。

Blazor 客户端应用将发布到 /bin/Release/{TARGET FRAMEWORK}/dist  文件夹。 Blazor 服务器端应用将发布到 /bin/Release/{TARGET FRAMEWORK}/publish  文件夹。

文件夹中的资产将部署到 Web 服务器。 部署可能是手动或自动化过程，具体取决于使用的开发工具。

## <a name="deployment"></a>部署

有关部署指南，请参阅以下主题：

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a>使用 Azure 存储实现 Blazor 无服务器托管

可以使用 [Azure 存储](https://azure.microsoft.com/services/storage/)将 Blazor 客户端应用作为直接来自存储容器的静态内容提供。

有关详细信息，请参阅[托管和部署 Blazor 客户端（独立部署）：Azure 存储](xref:host-and-deploy/blazor/client-side#azure-storage)。
