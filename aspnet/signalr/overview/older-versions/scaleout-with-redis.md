---
uid: signalr/overview/older-versions/scaleout-with-redis
title: 使用 Redis 的 SignalR 横向扩展 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 6abecf80-8ffa-41ba-b0d9-1d9edbe7687b
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 74294bd04d5649f2ec54e58adb744f5e30525162
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836410"
---
<a name="signalr-scaleout-with-redis-signalr-1x"></a>使用 Redis 的 SignalR 横向扩展 (SignalR 1.x)
====================
通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

在本教程中，您将使用[Redis](http://redis.io/)若要部署在两个单独的 IIS 实例的 SignalR 应用程序间分发消息。

Redis 是内存中键 / 值存储。 它还支持发布/订阅模型的消息传送系统。 Redis 的 SignalR 基架使用发布/订阅功能以将消息转发到其他服务器。

![](scaleout-with-redis/_static/image1.png)

对于本教程中，将使用三个服务器：

- 两台运行 Windows，将使用的 SignalR 应用程序部署的服务器。
- 一台运行 Linux，你将用于运行 Redis 服务器。 在本教程中的屏幕截图，我使用 Ubuntu 12.04 TLS。

如果没有要使用的三个物理服务器，您可以在 HYPER-V 上创建 Vm。 另一个选项是在 Azure 上创建 Vm。

虽然本教程使用官方 Redis 实现，此外，还有[Windows 端口的 Redis](https://github.com/MSOpenTech/redis)从 MSOpenTech。 安装和配置不相同，但否则步骤是相同的。

> [!NOTE] 
> 
> 使用 Redis 的 SignalR 横向扩展不支持 Redis 群集。


## <a name="overview"></a>概述

我们进入详细教程之前，下面是将执行哪些操作的快速概述。

1. 安装 Redis 并启动 Redis 服务器。
2. 将以下 NuGet 包添加到你的应用程序： 

    - [Microsoft.AspNet.SignalR](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [Microsoft.AspNet.SignalR.Redis](http://nuget.org/packages/Microsoft.AspNet.SignalR.Redis)
3. 创建 SignalR 应用程序。
4. 将以下代码添加到 Global.asax 配置基架： 

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a>Ubuntu 上的 HYPER-V

使用 Windows 的 HYPER-V，您可以轻松创建 Ubuntu VM 在 Windows Server 上。

下载从 Ubuntu ISO [ http://www.ubuntu.com ](http://www.ubuntu.com/)。

在 HYPER-V 中添加新的 VM。 在中**连接虚拟硬盘**步骤中，选择**创建虚拟硬盘**。

![](scaleout-with-redis/_static/image2.png)

在中**安装选项**步骤中，选择**映像文件 (.iso)**，单击**浏览**，并浏览到 Ubuntu 安装 ISO。

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a>安装 Redis

遵循的步骤[ http://redis.io/download ](http://redis.io/download)以下载和生成 Redis。

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

这会生成 Redis 二进制文件`src`目录。

默认情况下，Redis 不需要密码。 若要设置密码，请编辑`redis.conf`文件的源代码的根目录中。 （请文件的备份副本之前对其进行编辑 ！）添加以下指令为`redis.conf`:

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

现在，启动 Redis 服务器：

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

打开端口 6379，这是 Redis 的默认端口上侦听。 （可以更改配置文件中的端口号。）

## <a name="create-the-signalr-application"></a>创建 SignalR 应用程序

创建 SignalR 应用程序按照以下教程之一：

- [SignalR 入门](../getting-started/tutorial-getting-started-with-signalr.md)
- [使用 SignalR 和 MVC 4 入门](tutorial-getting-started-with-signalr-and-mvc-4.md)

接下来，我们将修改聊天应用程序，以支持采用 Redis 的扩展。 首先，将 SignalR.Redis NuGet 包添加到你的项目。 在 Visual Studio 中，从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。 在包管理器控制台窗口中，输入以下命令：

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

接下来，打开 Global.asax 文件。 将以下代码添加到**应用程序\_启动**方法：

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- "服务器"是运行 Redis 服务器的名称。
- *端口*是端口号
- "密码"是 redis.conf 文件中定义的密码。
- "AppName"是任何字符串。 SignalR 具有此名称创建的 Redis 发布/订阅通道。

例如：

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a>部署和运行应用程序

准备 Windows Server 实例将 SignalR 应用程序部署。

添加 IIS 角色。 包含"应用程序开发"功能，包括 WebSocket 协议。

![](scaleout-with-redis/_static/image5.png)

此外包括管理服务 （在"管理工具"列出）。

![](scaleout-with-redis/_static/image6.png)

**安装 Web 部署 3.0。** 当你运行 IIS 管理器，它将提示你安装 Microsoft Web 平台，或者可以[下载 intstaller](https://go.microsoft.com/fwlink/?LinkId=255386)。 在平台安装程序中，搜索 Web 部署并安装 Web Deploy 3.0

![](scaleout-with-redis/_static/image7.png)

检查 Web 管理服务正在运行。 如果没有，请启动服务。 （如果在 Windows 服务列表中，看不到 Web 管理服务，请确保添加 IIS 角色时安装管理服务。）

默认情况下，Web 管理服务侦听 TCP 端口 8172。 在 Windows 防火墙中创建一个新的入站的规则以允许端口 8172 上的 TCP 流量。 有关详细信息，请参阅[配置防火墙规则](https://technet.microsoft.com/library/dd448559(WS.10).aspx)。 （如果托管在 Azure 上的 Vm，你可以直接在 Azure 门户中。 请参阅[如何设置虚拟机的终结点](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/)。)

现在已准备好部署到服务器从开发计算机的 Visual Studio 项目。 在解决方案资源管理器，右键单击解决方案，然后单击**发布**。

有关更详细的有关 web 部署的文档，请参阅[用于 Visual Studio 和 ASP.NET 的 Web 部署内容映射](../../../whitepapers/aspnet-web-deployment-content-map.md)。

如果部署到两个服务器应用程序，可以在一个单独的浏览器窗口中打开每个实例，并查看它们每个接收来自其他 SignalR 消息。 （当然，在生产环境中，两个服务器将位于负载均衡器后面。）

![](scaleout-with-redis/_static/image8.png)

如果您感兴趣，查看将发送到 Redis，可以使用的消息**redis cli**安装使用 Redis 的客户端。

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)
