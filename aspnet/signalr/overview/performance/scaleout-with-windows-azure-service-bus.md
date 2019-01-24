---
uid: signalr/overview/performance/scaleout-with-windows-azure-service-bus
title: 使用 Azure 服务总线的 SignalR 横向扩展 |Microsoft Docs
author: bradygaster
description: 软件版本在此主题的 Visual Studio 2013.NET 4.5 SignalR 版本中使用本主题中，此主题的 SignalR 1.x 版本 2 早期版本...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: ce1305f9-30fd-49e3-bf38-d0a78dfb06c3
msc.legacyurl: /signalr/overview/performance/scaleout-with-windows-azure-service-bus
msc.type: authoredcontent
ms.openlocfilehash: 9f6188ff5f716c20d759f73975d6a8ad522834d8
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836813"
---
<a name="signalr-scaleout-with-azure-service-bus"></a>使用 Azure 服务总线的 SignalR 横向扩展
====================
通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

在本教程中，你将部署到 Windows Azure Web 角色，使用服务总线底板来将消息分散到每个角色实例的 SignalR 应用程序。 (还可以使用与服务总线底板[Azure 应用服务中的 web 应用](https://docs.microsoft.com/azure/app-service-web/)。)

![](scaleout-with-windows-azure-service-bus/_static/image1.png)

先决条件：

- Windows Azure 帐户。
- [Windows Azure SDK](https://go.microsoft.com/fwlink/?linkid=254364&amp;clcid=0x409)。
- Visual Studio 2012 或 2013年。

服务总线底板也是与兼容[Service Bus for Windows Server](https://msdn.microsoft.com/library/windowsazure/dn282144.aspx)，版本 1.1。 但是，不与 Service Bus for Windows Server 的 1.0 版兼容。

## <a name="pricing"></a>定价

服务总线底板使用主题发送消息。 有关最新定价信息，请参阅[服务总线](https://azure.microsoft.com/pricing/details/service-bus/)。 在撰写本文时，可以将每月 1,000,000 条消息发送小于 1 美元。 底板发送 SignalR 集线器方法的每个调用的服务总线消息。 也有一些控制消息的连接、 断开连接、 加入或离开组，等等。 在大多数应用程序，大多数消息流量将集线器方法调用。

## <a name="overview"></a>概述

我们进入详细教程之前，下面是将执行哪些操作的快速概述。

1. 使用 Windows Azure 门户创建新的服务总线命名空间。
2. 将以下 NuGet 包添加到你的应用程序： 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.ServiceBus3](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus3)或[Microsoft.AspNet.SignalR.ServiceBus](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.ServiceBus)
3. 创建 SignalR 应用程序。
4. 将以下代码添加到 Startup.cs 配置基架： 

    [!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample1.cs)]

此代码使用的默认值配置底板[TopicCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.servicebusscaleoutconfiguration.topiccount(v=vs.118).aspx)并[MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx)。 有关更改这些值的信息，请参阅[SignalR 性能：横向扩展指标](signalr-performance.md#scaleout_metrics)。

对于每个应用程序，为"YourAppName"选择一个不同的值。 跨多个应用程序不使用相同的值。

## <a name="create-the-azure-services"></a>创建 Azure 服务

创建云服务，如中所述[如何创建和部署云服务](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy)。 按照部分中的步骤"如何：创建云服务使用快速创建"。 对于本教程，您不需要上传的证书。

![](scaleout-with-windows-azure-service-bus/_static/image2.png)

创建新的服务总线命名空间中所述[如何使用服务总线主题/订阅到](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)。 按照"创建服务 Namespace"一节中的步骤。

![](scaleout-with-windows-azure-service-bus/_static/image3.png)

> [!NOTE]
> 请确保选择用于云服务和服务总线命名空间的同一区域。


## <a name="create-the-visual-studio-project"></a>创建 Visual Studio 项目

启动 Visual Studio。 从**文件**菜单上，单击**新项目**。

在中**新的项目**对话框框中，展开**Visual C#**。 下**已安装的模板**，选择**云**，然后选择**Windows Azure 云服务**。 保留默认.NET Framework 4.5。 命名应用程序 ChatService，然后单击**确定**。

![](scaleout-with-windows-azure-service-bus/_static/image4.png)

在中**新的 Windows Azure 云服务**对话框中，选择 ASP.NET Web 角色。 单击右箭头按钮 (**&gt;**) 将该角色添加到你的解决方案。

鼠标悬停在该新角色，因此可见的铅笔图标。 单击此图标可将角色重命名。 命名"SignalRChat"的角色，然后单击**确定**。

![](scaleout-with-windows-azure-service-bus/_static/image5.png)

在中**新建 ASP.NET 项目**对话框中，选择**MVC**，单击确定。

![](scaleout-with-windows-azure-service-bus/_static/image6.png)

项目向导将创建两个项目：

- ChatService:此项目是 Windows Azure 应用程序。 它定义的 Azure 角色和其他配置选项。
- SignalRChat:此项目是 ASP.NET MVC 5 项目。

## <a name="create-the-signalr-chat-application"></a>创建 SignalR 聊天应用程序

若要创建的聊天应用程序，按照本教程中的步骤[SignalR 和 MVC 5 入门](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)。

使用 NuGet 安装所需的库。 从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。 在中**程序包管理器控制台**窗口中，输入以下命令：

[!code-powershell[Main](scaleout-with-windows-azure-service-bus/samples/sample2.ps1)]

使用`-ProjectName`选项以将包安装到 ASP.NET MVC 项目中，而不是 Windows Azure 项目。

## <a name="configure-the-backplane"></a>配置底板

在应用程序的 Startup.cs 文件中，添加以下代码：

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample3.cs)]

现在，你需要获取服务总线连接字符串。 在 Azure 门户中，选择你创建的服务总线命名空间并单击访问密钥图标。

![](scaleout-with-windows-azure-service-bus/_static/image7.png)

将连接字符串复制到剪贴板，然后将其粘贴到*connectionString*变量。

![](scaleout-with-windows-azure-service-bus/_static/image8.png)

[!code-csharp[Main](scaleout-with-windows-azure-service-bus/samples/sample4.cs)]

## <a name="deploy-to-azure"></a>部署到 Azure

在解决方案资源管理器，展开**角色**ChatService 项目内的文件夹。

![](scaleout-with-windows-azure-service-bus/_static/image9.png)

右键单击 SignalRChat 角色并选择**属性**。 选择**配置**选项卡。下**实例**选择 2。 此外可以将 VM 大小设置为**特小**。

![](scaleout-with-windows-azure-service-bus/_static/image10.png)

保存更改。

在解决方案资源管理器，右键单击 ChatService 项目。 选择“发布”。

![](scaleout-with-windows-azure-service-bus/_static/image11.png)

如果这是你第一次发布到 Windows Azure，你必须下载您的凭据。 在中**发布**向导中，单击"登录以下载凭据"。 这将提示你登录到 Windows Azure 门户并下载发布设置文件。

![](scaleout-with-windows-azure-service-bus/_static/image12.png)

单击**导入**和选择所下载的发布设置文件。

单击 **“下一步”**。 在中**发布设置**对话框下**云服务**，选择前面创建的云服务。

![](scaleout-with-windows-azure-service-bus/_static/image13.png)

单击“发布” 。 可能需要几分钟才能部署该应用程序和启动 Vm。

现在运行聊天应用程序时，角色实例将通过使用服务总线主题的 Azure 服务总线通信。 本主题是允许多个订阅服务器的消息队列。

基架会自动创建主题和订阅。 若要查看的订阅和消息活动，打开 Azure 门户、 选择服务总线命名空间，并单击"主题"。

![](scaleout-with-windows-azure-service-bus/_static/image14.png)

它使需要几分钟才会显示在仪表板中的消息活动。

![](scaleout-with-windows-azure-service-bus/_static/image15.png)

SignalR 管理主题生存期。 只要部署应用程序，请勿尝试手动删除主题或主题上更改设置。

## <a name="troubleshooting"></a>疑难解答

**System.InvalidOperationException"唯一受支持的隔离级别是 IsolationLevel.Serializable'。"**

如果某个操作的事务级别设置为内容而不会出现此错误`Serializable`。 验证与其他事务级别正在执行任何操作。
