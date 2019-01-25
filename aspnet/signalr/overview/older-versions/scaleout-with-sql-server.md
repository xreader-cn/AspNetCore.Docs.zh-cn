---
uid: signalr/overview/older-versions/scaleout-with-sql-server
title: 使用 SQL Server 的 SignalR 横向扩展 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 1dca7967-8296-444a-9533-837eb284e78c
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 7fd05a4487be4cc96fa945cf08226841e3f01806
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835930"
---
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a>使用 SQL Server 的 SignalR 横向扩展 (SignalR 1.x)
====================
通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

在本教程中，将使用 SQL Server 将消息分布到两个单独的 IIS 实例中部署的 SignalR 应用程序。 此外可以在单个测试计算机上，运行本教程中，但若要获取完整的效果，您需要 SignalR 应用程序部署到两个或多个服务器。 其中一台服务器，或单独的专用服务器上，还必须安装 SQL Server。 另一种方法是运行本教程使用 Azure 上的 Vm。

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a>系统必备

Microsoft SQL Server 2005 或更高版本。 底板支持桌面和服务器版本的 SQL Server。 不支持 SQL Server Compact Edition 或 Azure SQL 数据库。 （如果你的应用程序托管在 Azure 上，考虑服务总线底板。）

## <a name="overview"></a>概述

我们进入详细教程之前，下面是将执行哪些操作的快速概述。

1. 创建新的空数据库。 此数据库中，基架将创建所需的表。
2. 将以下 NuGet 包添加到你的应用程序： 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.SqlServer](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. 创建 SignalR 应用程序。
4. 将以下代码添加到 Global.asax 配置基架： 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a>配置数据库

确定应用程序是否将使用 Windows 身份验证或 SQL Server 身份验证来访问数据库。 无论如何，请确保数据库用户有权登录，创建架构，并创建表。

创建基架以使用新的数据库。 可以为数据库提供任何名称。 无需在数据库中; 中创建的任何表基架将创建所需的表。

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a>启用 Service Broker

建议为底板数据库启用服务中介程序。 Service Broker 提供了对消息传送和队列在 SQL Server，可让更高效地接收更新底板中的本机支持。 （但是，基架也无需正常 Service Broker。）

若要检查是否启用 Service Broker，请查询**是\_broker\_启用**中的列**sys.databases**目录视图。

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

若要启用服务中介程序，请使用以下 SQL 查询：

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> 如果此查询出现死锁，请确保没有应用程序连接到数据库。

如果已启用跟踪，跟踪还会显示是否启用 Service Broker。

## <a name="create-a-signalr-application"></a>创建 SignalR 应用程序

创建 SignalR 应用程序按照以下教程之一：

- [SignalR 入门](../getting-started/tutorial-getting-started-with-signalr.md)
- [使用 SignalR 和 MVC 4 入门](tutorial-getting-started-with-signalr-and-mvc-4.md)

接下来，我们将修改要支持使用 SQL Server 横向扩展的聊天应用程序。 首先，将 SignalR.SqlServer NuGet 包添加到你的项目。 在 Visual Studio 中，从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。 在包管理器控制台窗口中，输入以下命令：

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

接下来，打开 Global.asax 文件。 将以下代码添加到**应用程序\_启动**方法：

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a>部署和运行应用程序

准备 Windows Server 实例将 SignalR 应用程序部署。

添加 IIS 角色。 包含"应用程序开发"功能，包括 WebSocket 协议。

![](scaleout-with-sql-server/_static/image4.png)

此外包括管理服务 （在"管理工具"列出）。

![](scaleout-with-sql-server/_static/image5.png)

**安装 Web 部署 3.0。** 当你运行 IIS 管理器，它将提示你安装 Microsoft Web 平台，或者可以[下载 intstaller](https://go.microsoft.com/fwlink/?LinkId=255386)。 在平台安装程序中，搜索 Web 部署并安装 Web Deploy 3.0

![](scaleout-with-sql-server/_static/image6.png)

检查 Web 管理服务正在运行。 如果没有，请启动服务。 （如果在 Windows 服务列表中，看不到 Web 管理服务，请确保添加 IIS 角色时安装管理服务。）

最后，打开 TCP 端口 8172。 这是 Web 部署工具使用的端口。

现在已准备好部署到服务器从开发计算机的 Visual Studio 项目。 在解决方案资源管理器，右键单击解决方案，然后单击**发布**。

有关更详细的有关 web 部署的文档，请参阅[用于 Visual Studio 和 ASP.NET 的 Web 部署内容映射](../../../whitepapers/aspnet-web-deployment-content-map.md)。

如果部署到两个服务器应用程序，可以在一个单独的浏览器窗口中打开每个实例，并查看它们每个接收来自其他 SignalR 消息。 （当然，在生产环境中，两个服务器将位于负载均衡器后面。）

![](scaleout-with-sql-server/_static/image7.png)

运行应用程序后，可以看到 SignalR 自动具有在数据库中创建表：

![](scaleout-with-sql-server/_static/image8.png)

SignalR 管理表。 只要部署应用程序，不删除行、 修改表，等等。
