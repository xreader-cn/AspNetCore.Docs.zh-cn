---
uid: signalr/overview/older-versions/signalr-performance
title: SignalR 性能 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: SignalR 性能
ms.author: bradyg
ms.date: 07/03/2013
ms.assetid: 9594d644-66b6-4223-acdd-23e29a6e4c46
msc.legacyurl: /signalr/overview/older-versions/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: 4158cb055088f3da752020e577007ffe80856b60
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837723"
---
<a name="signalr-performance-signalr-1x"></a>SignalR 性能 (SignalR 1.x)
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本主题介绍如何设计、 测量和提高性能的 SignalR 应用程序中。


SignalR 性能和缩放上的最新演示文稿，请参阅[缩放使用 ASP.NET SignalR 的实时 Web](https://channel9.msdn.com/Events/Build/2013/3-502)。

本主题包含以下各节：

- [设计注意事项](#design)
- [优化 SignalR 服务器性能](#tuning)
- [故障排除性能问题](#troubleshooting)
- [使用 SignalR 性能计数器](#perfcounters)
- [使用其他性能计数器](#othercounters)
- [其他资源](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a>设计注意事项

本部分介绍可实现 SignalR 应用程序，以确保性能不正在通过生成不必要的网络流量受阻的设计过程中的模式。

### <a name="throttling-message-frequency"></a>限制消息的频率

即使在发送消息 （如实时游戏应用程序） 的高频率的应用程序，大多数应用程序不需要发送第二个较多消息。 若要减少的每个客户端生成的流量，一个消息循环可以实现队列并将其发送出消息不会有经常超过费率是固定的 （即，最多数量的消息将发送每隔一秒，如果在该时间的消息terval 发送)。 限制到特定的率 （从客户端和服务器） 的消息的示例应用程序，请参阅[高频率实时功能与 SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md)。

### <a name="reducing-message-size"></a>减少消息大小

可以通过减少的序列化对象的大小来减少 SignalR 消息的大小。 在服务器代码中，如果要发送一个对象，包含要传输不需要的属性，防止这些属性序列化的使用`JsonIgnore`属性。 属性的名称也存储在消息;可以使用缩写的属性名称`JsonProperty`属性。 下面的代码示例演示了如何排除某属性发送到客户端，以及如何缩短属性名称：

**.NET 服务器代码，演示 JsonIgnore 属性发送到客户端，排除数据和 JsonProperty 属性来减少消息大小**

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

若要保留可读性 / 客户端代码中的可维护性，在收到消息后，缩写的属性名称可以是重新映射到用户友好名称。 下面的代码示例演示如何重新映射到较长的通过定义消息协定 （映射） 的缩写的名称的可能方法以及使用`reMap`函数以将协定应用于优化的消息类：

**将重新映射的客户端 JavaScript 代码缩短到用户可读名称的属性名称**

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

名称可以缩短到服务器，客户端的消息中使用相同的方法。

降低内存占用量 （即，消息所使用的内存量） 消息的对象还可以提高性能。 例如，如果整套`int`不需要`short`或`byte`可改为使用。

因为消息存储在服务器内存，从而减少消息的大小在消息总线中还可以解决服务器内存问题。

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a>优化 SignalR 服务器性能

可以使用以下配置设置来优化你的服务器的 SignalR 应用程序中更好的性能。 有关如何改进 ASP.NET 应用程序的性能的常规信息，请参阅[提高 ASP.NET 性能](https://msdn.microsoft.com/library/ff647787.aspx)。

**SignalR 配置设置**

- **DefaultMessageBufferSize**:默认情况下，SignalR 将保留在内存中的每个中心的每个连接的 1000 条消息。 如果使用大型消息时，这可能会造成内存问题，这可以通过减小此值来缓解这。 此设置可以设置`Application_Start`事件处理程序在 ASP.NET 应用程序，或在`Configuration`自承载的应用程序中的 OWIN 启动类的方法。 下面的示例演示如何以减少应用程序，以减少使用的服务器内存量的内存占用减小此值：

    **.NET 服务器代码在 Global.asax 中减少默认消息缓冲区大小**

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

**IIS 配置设置**

- **每个应用程序的最大并发请求**:增加并发 IIS 请求会增加可用于为请求提供服务的服务器资源。 默认值为 5000;若要增加此设置，请在提升的命令提示符中执行以下命令：

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]

**ASP.NET 配置设置**

本部分包括可以在设置的配置设置`aspnet.config`文件。 在两个位置，具体取决于平台之一中找到此文件：

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

可能会提高 SignalR 性能的 ASP.NET 设置包括：

- **每个 CPU 的最大并发请求**:增加此设置可能会缓解性能瓶颈。 若要增加此设置，添加以下配置设置来`aspnet.config`文件：

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- **请求队列限制**:当连接总数超过`maxConcurrentRequestsPerCPU`设置，ASP.NET 将开始限制使用队列的请求。 若要增加队列的大小，可以增加`requestQueueLimit`设置。 若要执行此操作，将添加到以下配置设置`processModel`中的节点`config/machine.config`(而非`aspnet.config`):

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a>故障排除性能问题

本部分介绍如何在应用程序中找出性能瓶颈。

### <a name="verifying-that-websocket-is-being-used"></a>验证正在使用 WebSocket

虽然 SignalR 可以使用各种传输方式，客户端和服务器之间的通信，WebSocket 提供显著的性能优势，并且应在客户端和服务器支持它。 若要确定客户端和服务器是否满足 WebSocket 的要求，请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)。 若要确定你的应用程序中正在使用什么传输，可以使用浏览器开发人员工具，并检查日志以查看正在使用什么传输连接。 有关使用 Internet Explorer 和 Chrome 中的浏览器开发工具的信息，请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)。

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a>使用 SignalR 性能计数器

本部分介绍如何启用和使用 SignalR 性能计数器中找到`Microsoft.AspNet.SignalR.Utils`包。

### <a name="installing-signalrexe"></a>安装 signalr.exe

可以使用名为 SignalR.exe 的实用工具向服务器添加性能计数器。 若要安装此实用程序，请按照下列步骤：

1. 在 Visual Studio 中，选择**工具** > **NuGet 包管理器** > **为解决方案管理 NuGet 包**
2. 搜索**signalr.utils**，并选择安装。

    ![](signalr-performance/_static/image1.png)
3. 接受许可协议才能安装包。
4. 将安装 SignalR.exe `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`。

### <a name="installing-performance-counters-with-signalrexe"></a>使用 SignalR.exe 安装性能计数器

若要安装 SignalR 性能计数器，请在提升的命令提示符使用以下参数运行 SignalR.exe:

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

若要删除 SignalR 性能计数器，请在提升的命令提示符使用以下参数运行 SignalR.exe:

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a>SignalR 性能计数器

实用程序包将安装以下性能计数器。 "总数"计数器度量值事件的数，因为最后一个应用程序池或服务器重新启动。

**连接指标**

以下指标测量发生的连接生存期事件。 有关详细信息，请参阅[理解和处理的连接生存期事件](../guide-to-the-api/handling-connection-lifetime-events.md)。

- **连接的连接**
- **重新连接的连接**
- **连接断开**
- **连接当前**

**消息指标**

以下指标测量生成 SignalR 的消息流量。

- **连接消息接收的总数**
- **发送的总连接消息**
- **连接接收的消息/秒**
- **连接发送的消息/秒**

**消息总线指标**

以下指标测量流量通过内部 SignalR 消息总线，位于所有传入和传出 SignalR 消息的队列。 一条消息是**已发布**发送或广播时。 一个**订阅服务器**在此上下文是在消息总线订阅; 这应等于客户端和服务器本身的数量。 **分配辅助角色**是一个组件，将数据发送到活动连接;**繁忙工作线程**是指正在主动发送一条消息。

- **消息总线的消息接收的总数**
- **消息总线每秒接收的消息**
- **消息总线的消息发布总数**
- **消息总线的消息发布数/秒**
- **消息总线订阅服务器当前**
- **消息总线订阅者总数**
- **消息总线订阅者/秒**
- **消息总线分配辅助角色**
- **消息总线繁忙工作线程**
- **消息总线主题当前**

**错误指标**

以下指标测量生成的 SignalR 消息通信错误。 **集线器解析**中心或中心方法无法解析时出现错误。 **集线器调用**错误是调用集线器方法时引发的异常。 **传输**错误是在 HTTP 请求或响应过程中引发的连接错误。

- **错误：所有总数**
- **错误：All/Sec**
- **错误：中心解决方法总数**
- **错误：每秒集线器解析**
- **错误：集线器调用总数**
- **错误：Hub Invocation/Sec**
- **错误：传输总数**
- **错误：传输/秒**

**横向扩展指标**

以下指标测量流量和横向扩展提供程序生成错误。 一个**Stream**在此上下文中的缩放单位使用的扩展提供的; 这是一个表，如果使用 SQL Server、 使用服务总线时，如果一个主题和订阅如果使用 Redis。 默认情况下，使用只有一个流，但增大此值可以通过在 SQL Server 和服务总线上的配置。 一个**缓冲**流是指已进入错误的状态; 当流处于错误状态，所有消息发送到底板之前，都无法立即流不能再处理故障。 **发送队列长度**是已发送但尚未发送的消息数。

- **横向扩展消息总线消息接收数/秒**
- **横向扩展流总计**
- **横向扩展流打开**
- **横向扩展流缓冲**
- **横向扩展错误总数**
- **每秒扩展错误**
- **横向扩展发送队列长度**

这些计数器所度量的详细信息，请参阅[与 Azure 服务总线的 SignalR 横向扩展](scaleout-with-windows-azure-service-bus.md)。

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a>使用其他性能计数器

以下性能计数器也可能用于监视应用程序的性能。

**内存**

- 所有堆 （w3wp) 中的字节.NET CLR 内存数

**ASP.NET 2.0**

- Asp.net\requests Current
- ASP.NET\Queued
- ASP.NET\Rejected

**CPU**

- 处理器 Information\Processor 时间

**TCP/IP**

- TCPv6/已建立的连接
- TCPv4/已建立的连接

**Web 服务**

- Web 服务 \ 当前连接
- Web Service\Maximum Connections

**线程处理**

- .NET CLR LocksAndThreads\#的当前逻辑线程
- .NET CLR LocksAnd 线程\#的当前物理线程

<a id="otherresources"></a>

## <a name="other-resources"></a>其他资源

ASP.NET 性能监视和优化的详细信息，请参阅以下主题：

- [ASP.NET 性能概述](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)
- [IIS 7.5、 IIS 7.0 和 IIS 6.0 上的 ASP.NET 线程使用情况](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [&lt;applicationPool&gt;元素 （Web 设置）](https://msdn.microsoft.com/library/dd560842.aspx)
