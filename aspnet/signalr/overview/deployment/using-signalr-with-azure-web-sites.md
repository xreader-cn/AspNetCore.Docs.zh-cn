---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: 在 Azure 应用服务中的 Web 应用中使用 SignalR |Microsoft Docs
author: bradygaster
description: 本文档介绍如何配置运行在 Microsoft Azure 的 SignalR 应用程序。 在教程的 Visual Studio 2013 或 Vis.中使用的软件版本...
ms.author: bradyg
ms.date: 07/01/2015
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: 13eb5d29a2c40f52aed4b569ec8695f014a05f03
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837697"
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a>在 Azure 应用服务中的 Web 应用中使用 SignalR
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文档介绍如何配置运行在 Microsoft Azure 的 SignalR 应用程序。
>
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)或 Visual Studio 2012
> - .NET 4.5
> - SignalR 版本 2
> - 用于 Visual Studio 2013 或 2012年的 azure SDK 2.3
>
>
>
> ## <a name="questions-and-comments"></a>问题和提出的意见
>
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)， [StackOverflow.com](http://stackoverflow.com/)，或[Microsoft Azure 论坛](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).


## <a name="table-of-contents"></a>目录

- [介绍](#introduction)
- [SignalR Web 应用部署到 Azure 应用服务](#deploying)
- [Azure 应用服务上启用 Websocket](#websocket)
- [使用 Azure Redis 缓存底板](#backplane)
- [后续步骤](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a>介绍

可以使用 ASP.NET SignalR 将一个新的服务器和 web 或.NET 客户端之间的交互级别。 Azure 中托管时，SignalR 应用程序可以充分利用高度可用、 可缩放，并在云中运行的提供高性能环境。

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a>SignalR Web 应用部署到 Azure 应用服务

SignalR 不会添加到 Azure 的应用程序部署与部署到的本地服务器的任何特定的复杂情况。 可以在 Azure 中无需更改任何配置或其他设置中托管的应用程序使用 SignalR (但 Websocket 支持，请参阅[Azure 应用服务上启用 WebSockets](#websocket)下面。)对于本教程中，您需要将中创建的应用程序部署[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)到 Azure。

**系统必备**

- Visual Studio 2013. 如果未安装 Visual Studio，Visual Studio 2013 Express for Web 是在安装中包括的 Azure sdk。
- [用于 Visual Studio 2013 的 azure SDK 2.3](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)或[适用于 Visual Studio 2012 的 Azure SDK 2.3](https://go.microsoft.com/fwlink/p/?linkid=323511)。
- 若要完成本教程中，将需要 Azure 订阅。 你可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，或[注册试用版订阅](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="deploying-a-signalr-web-app-to-azure"></a>SignalR web 应用部署到 Azure

1. 完成[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)，或下载中完成的项目[代码库](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)。
2. 在 Visual Studio 中，选择**构建**，**发布 SignalR 聊天**。
3. 在"发布 Web"对话框中，选择"Windows Azure 网站"。

    ![选择 Azure 网站](using-signalr-with-azure-web-sites/_static/image1.png)
4. 如果您没有登录到你的 Microsoft 帐户，请单击**登录...** 中"选择现有网站"对话框中，并登录。

    ![选择现有网站](using-signalr-with-azure-web-sites/_static/image2.png)    ![登录 Azure](using-signalr-with-azure-web-sites/_static/image3.png)
5. 在"选择现有网站"对话框中，单击**新建**。

    ![新建网站](using-signalr-with-azure-web-sites/_static/image4.png)
6. 在"Windows Azure 上的创建网站"对话框中，输入唯一的应用名称。 在区域下拉列表中选择离你最近的区域。 单击 **“创建”**。

    ![在 Azure 中创建网站](using-signalr-with-azure-web-sites/_static/image5.png)
7. 在"发布 Web"对话框中，单击**发布**。

    ![发布站点](using-signalr-with-azure-web-sites/_static/image6.png)
8. 应用完成发布后，将在浏览器中打开托管在 Azure 应用服务 Web 应用中的 SignalR 聊天应用程序。

    ![在浏览器中打开站点](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a>在 Azure 应用服务 Web 应用启用 Websocket

Websocket 需要显式启用 web 应用程序中使用 SignalR 应用程序; 中否则，将使用其他协议 (请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)有关详细信息)。

若要在 Azure 应用服务 Web 应用使用 Websocket，启用它的 web 应用的配置部分中。 若要执行此操作，请打开中的 web 应用[Azure 管理门户](https://manage.windowsazure.com/)，并选择配置。

![配置选项卡](using-signalr-with-azure-web-sites/_static/image8.png)

在配置页的顶部，请确保你的 web 应用，使用.NET 4.5。

![.NET framework 版本 4.5 设置](using-signalr-with-azure-web-sites/_static/image9.png)

在配置页上，在**Websocket**设置中，选择**上**。

![Websocket 的设置：On](using-signalr-with-azure-web-sites/_static/image10.png)

在配置页的底部，选择**保存**以保存所做的更改。

![保存设置](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a>使用 Azure Redis 缓存底板

如果为 web 应用，使用多个实例，这些实例的用户需要彼此交互，以便，例如，创建一个实例中的聊天消息可以访问用户连接到其他实例），则[Azure Redis 缓存底板](../performance/scaleout-with-redis.md)必须在你的应用程序中实现。

<a id="nextsteps"></a>
## <a name="next-steps"></a>后续步骤

在 Azure 应用服务中 Web 应用的详细信息，请参阅[Web 应用概述](https://azure.microsoft.com/documentation/articles/app-service-web-overview/)。
