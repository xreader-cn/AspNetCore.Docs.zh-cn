---
uid: signalr/overview/performance/signalr-connection-density-testing-with-crank
title: 测试使用曲柄实现 SignalR 连接密度 |Microsoft Docs
author: bradygaster
description: 测试使用曲柄实现 SignalR 连接密度
ms.author: bradyg
ms.date: 02/22/2015
ms.assetid: 148d9ca7-1af1-44b6-a9fb-91e261b9b463
msc.legacyurl: /signalr/overview/performance/signalr-connection-density-testing-with-crank
msc.type: authoredcontent
ms.openlocfilehash: 40c9764f0c47b83df8300553b4b290429937345c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836540"
---
<a name="signalr-connection-density-testing-with-crank"></a>测试使用曲柄实现 SignalR 连接密度
====================
通过[Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文介绍如何使用曲柄工具来测试具有多个模拟客户端的应用程序。


在应用程序运行在其宿主环境中 （Azure web 角色，IIS，或使用 Owin 自承载） 中，可以测试应用程序的响应较高的使用曲柄工具的连接密度级别。 宿主环境可以是 Internet 信息服务 (IIS) 服务器、 Owin 主机或 Azure web 角色。 （注意：性能计数器不可在 Azure 应用服务 Web 应用，因此你将无法再从连接密度测试获取性能数据。）

连接密度是指可以在服务器建立的并发 TCP 连接数。 每个 TCP 连接可能会产生其自身的开销，并打开大量的空闲连接最终会创建内存瓶颈。

[SignalR o d e b](https://github.com/signalr/signalr)包括负载测试工具，称为**启动**。 中找不到最新版本的曲柄[Dev 分支](https://github.com/SignalR/signalr/tree/dev)GitHub 上。 您可以下载的 Zip 存档的 SignalR 的 Dev 分支 o d e b[此处](https://github.com/SignalR/SignalR/archive/dev.zip)。

曲柄可用于完全饱和服务器的内存以便计算可能服务器硬件上的空闲连接的总数。 或者，你可能还通过曲柄进行负载测试一定量的内存压力下的服务器的提升效应连接直到到达特定计数或特定的内存阈值。

在测试时，务必使用远程客户端以避免任何竞争资源 （即，TCP 连接和内存）。 监视客户端以确保没有达到可能会阻止服务器达到其完整的容量 （内存或 CPU） 的任何瓶颈。 您可能需要增加以完全加载服务器的客户端的数量。

### <a name="running-a-connection-density-test"></a>运行连接密度测试

本部分介绍对 SignalR 应用程序运行连接密度测试所需的步骤。

1. 下载并构建[SignalR 的 Dev 分支 b a s e](https://github.com/SignalR/SignalR/archive/dev.zip)。 在命令提示符中，导航到&lt;项目目录&gt;\src\Microsoft.AspNet.SignalR.Crank\bin\debug。
2. 应用程序部署到其预期的宿主环境。 请记下的终结点的应用程序使用;例如，在中创建的应用程序[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)，该终结点是`http://<yourhost>:8080/signalr`。
3. 安装[SignalR 性能计数器](signalr-performance.md#perfcounters)在服务器上。 如果在 Azure 上运行你的应用程序，请参阅[在 Azure Web 角色中使用 SignalR 性能计数器](using-signalr-performance-counters-in-an-azure-web-role.md)。

曲柄命令行工具已下载和生成的基本代码，并在主机上安装性能计数器，可在`src\Microsoft.AspNet.SignalR.Crank\bin\Debug`文件夹。

曲柄工具的可用选项包括：

- **/?**:显示帮助屏幕。 如果也会显示可用的选项**Url**省略参数。
- **/ Url**:SignalR 连接 URL。 此参数是必需的。 使用默认映射为 SignalR 应用程序，该路径将在结尾"/ signalr"。
- **/ 传输**:传输使用的名称。 默认值是`auto`，这将选择最佳的可用协议。 选项包括`WebSockets`， `ServerSentEvents`，并`LongPolling`(`ForeverFrame`没有一个选项为曲柄，因为.NET 客户端而不是使用 Internet Explorer)。 SignalR 如何选择传输方式的详细信息，请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)。
- **/ BatchSize**:每个批处理中添加客户端数。 默认值为 50。
- **/ ConnectInterval**:间隔 （毫秒） 之间添加连接。 默认值为 500。
- **/ 连接**:用于负载测试应用程序的连接数。 默认值为 100,000。
- **/ ConnectTimeout**:以秒，然后中止测试的超时。 默认值为 300。
- **MinServerMBytes**:若要达到最小服务器兆字节。 默认值为 500。
- **SendBytes**:有效负载发送到服务器以字节为单位的大小。 默认值为 0。
- **SendInterval**:延迟的消息与服务器之间的毫秒数。 默认值为 500。
- **SendTimeout**:以毫秒为单位的到服务器的消息的超时。 默认值为 300。
- **ControllerUrl**:一台客户端将在其中托管控制器中心 Url。 默认值为 null （无控制器中心）。 控制器中心启动时启动曲柄会话;无需再进行控制器集线器和曲柄之间的联系。
- **NumClients**:模拟客户端连接到该应用程序数。 默认值为 1。
- **日志文件**:为测试运行日志文件的文件名。 默认值为 `crank.csv`。
- **SampleInterval**:时间 （毫秒） 之间的性能计数器样本。 默认值为 1000。
- **SignalRInstance**:在服务器上的性能计数器实例名称。 默认值是使用客户端连接状态。

### <a name="example"></a>示例

以下命令将测试名的网站`pfsignalr`在 Azure 上承载端口 8080 上的应用程序使用名为"ControllerHub"中心，使用 100 个连接。

`crank /Connections:100 /Url:http://pfsignalr.cloudapp.net:8080/signalr`
