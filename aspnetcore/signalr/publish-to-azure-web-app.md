---
title: 发布 ASP.NET Core SignalR 应用程序到 Azure 应用服务
author: bradygaster
description: 了解如何将 ASP.NET Core SignalR 应用发布到 Azure 应用服务。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406104"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>发布 ASP.NET Core SignalR 应用程序到 Azure 应用服务

通过[Brady Gaster](https://twitter.com/bradygaster)

[Azure 应用服务](/azure/app-service/app-service-web-overview)是[Microsoft 云计算](https://azure.microsoft.com/)平台服务，用于托管 web 应用，包括 ASP.NET Core。

> [!NOTE]
> 本文是指从 Visual Studio 发布 ASP.NET Core SignalR 应用程序。 有关详细信息，请参阅[Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service)。

## <a name="publish-the-app"></a>发布应用

本文介绍如何发布使用 Visual Studio 中的工具。 可以使用 visual Studio Code 用户[Azure CLI](/cli/azure)命令以将应用发布到 Azure。 有关详细信息，请参阅[使用命令行工具将 ASP.NET Core 应用发布到 Azure](/azure/app-service/app-service-web-get-started-dotnet)。

1. 在“解决方案资源管理器”  中右键单击该项目，然后选择“发布”  。

1. 确认**应用服务**并**新建**中选择**选取发布目标**对话框。

1. 选择**创建配置文件**从**发布**向下按钮拖放。

   输入中的以下表中所述的信息**创建应用服务**对话框并选择**创建**。

   | 项               | 描述 |
   | ------------------ | ----------- |
   | **名称**           | 应用程序的唯一名称。 |
   | **订阅**   | 该应用使用的 azure 订阅。 |
   | **资源组** | 应用程序所属的相关资源的组。 |
   | **托管计划**   | Web 应用的的定价计划。 |

1. 选择**Azure SignalR 服务**中**依赖关系** > **添加**下拉列表：

   ![添加下拉列表中显示所选内容的 Azure SignalR 服务的依赖项区域](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. 在中**Azure SignalR 服务**对话框中，选择**创建新的 Azure SignalR 服务实例**。

1. 提供**名称**，**资源组**，并**位置**。 返回到**Azure SignalR 服务**对话框并选择**添加**。

Visual Studio 将完成以下任务：

* 创建发布配置文件包含发布设置。
* 创建*的 Azure Web 应用*与提供的详细信息。
* 会将应用发布。
* 将启动浏览器中，这将加载 web 应用。

应用程序的 URL 的格式是`{APP SERVICE NAME}.azurewebsites.net`。 例如，名为的应用`SignalRChatApp`具有的 URL `https://signalrchatapp.azurewebsites.net`。

如果 HTTP *502.2-错误的网关*部署面向预览版.NET Core 发行版的应用时出现错误，请参阅[到 Azure 应用服务部署 ASP.NET Core 预览版本](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)解决方法。

## <a name="configure-the-app-in-azure-app-service"></a>在 Azure 应用服务中配置应用

> [!NOTE]
> *本部分仅适用于不使用 Azure SignalR 服务应用。*
>
> 如果应用使用 Azure SignalR 服务，应用服务不需要应用程序请求路由 (ARR) 关联和 Web 套接字在本部分中所述的配置。 Azure SignalR 服务，而不是直接应用程序，客户端连接其 Web 套接字。

对于托管的应用而无需 Azure SignalR 服务，启用：

* [ARR 相关性](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html)到回相同的应用服务实例路由来自用户的请求。 默认设置是**上**。
* [Web 套接字](xref:fundamentals/websockets)以允许 Web 套接字传输到该函数。 默认设置是**关闭**。

1. 在 Azure 门户中，导航到中的 web 应用**应用服务**。
1. 打开**配置** > **常规设置**。
1. 设置**Web 套接字**到**上**。
1. 确认**ARR 相关性**设置为**上**。

## <a name="app-service-plan-limits"></a>应用服务计划限制

Web 套接字和其他传输是限制基于所选的应用服务计划。 有关详细信息，请参阅*Azure 云服务限制*并*应用服务限制*的部分[Azure 订阅和服务限制、 配额和约束](/azure/azure-subscription-service-limits#app-service-limits)一文。

## <a name="additional-resources"></a>其他资源

* [什么是 Azure SignalR 服务？](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [使用命令行工具将 ASP.NET Core 应用发布到 Azure](/azure/app-service/app-service-web-get-started-dotnet)
* [承载并将其部署在 Azure 上的 ASP.NET Core 预览应用](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
