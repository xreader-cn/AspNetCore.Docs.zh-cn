---
uid: signalr/overview/getting-started/real-time-web-applications-with-signalr
title: 动手实验：使用 SignalR 实时 Web 应用程序 |Microsoft Docs
author: bradygaster
description: 实时 Web 应用程序功能推送服务器端的情况下，实时连接的客户端到内容的功能。 对于 ASP.NET 开发人员，ASP...
ms.author: bradyg
ms.date: 07/16/2014
ms.assetid: ba07958c-42e1-4da0-81db-ba6925ed6db0
msc.legacyurl: /signalr/overview/getting-started/real-time-web-applications-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: d4998c8b739b4b1a06699a17464a7399a87a8595
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837502"
---
<a name="hands-on-lab-real-time-web-applications-with-signalr"></a>动手实验：使用 SignalR 实时 Web 应用程序
====================

通过[Web 训练营团队](https://twitter.com/webcamps)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[下载 Web 训练营培训工具包](http://aka.ms/webcamps-training-kit)

> 实时 Web 应用程序功能推送服务器端的情况下，实时连接的客户端到内容的功能。 面向 ASP.NET 开发人员**ASP.NET SignalR**是一个库，以将实时 web 功能添加到其应用程序。 它利用多个传输协议，会自动选择给定客户端和服务器的最佳可用的传输最佳的可用传输。 它利用**WebSocket**，一个 HTML5 API，使浏览器和服务器之间的双向通信。
> 
> **SignalR**还执行服务器到客户端 RPC 提供简单、 高级 API （在客户端的浏览器从服务器端.NET 代码中调用 JavaScript 函数） 在 ASP.NET 应用程序，以及添加有用的挂钩，以便连接管理例如，连接/断开连接事件、 分组连接和授权。
> 
> **SignalR**通过某些要求进行客户端和服务器之间的实时工作的传输的是一种抽象。 一个**SignalR**连接为 HTTP，将启动，然后升级到**WebSocket**如果可用的连接。 **WebSocket**是用于的理想之选传输**SignalR**，因为这会导致服务器内存的最有效地使用具有最低的延迟，并且具有最基础的功能 (例如客户端之间的全双工通信和服务器），但它还具有最严格的要求：**WebSocket**要求要使用的服务器**Windows Server 2012**或**Windows 8**，连同 **.NET Framework 4.5**。 如果不满足这些要求， **SignalR**将尝试使用其他传输，以使其连接 (如*Ajax 长轮询*)。
> 
> **SignalR** API 包含客户端和服务器之间进行通信的两个模型：**持久连接**并**中心**。 一个**连接**表示一个简单的终结点发送单一接收方组合在一起，或广播消息。 一个**中心**是基于连接 API，从而使您的客户端和服务器直接相互调用方法的更多高级管道。
> 
> ![SignalR 体系结构](real-time-web-applications-with-signalr/_static/image1.png)
> 
> 在 Web 训练营培训工具包中，可在包含所有示例代码和代码段[ http://aka.ms/webcamps-training-kit ](http://aka.ms/webcamps-training-kit)。


<a id="Overview"></a>
## <a name="overview"></a>概述

<a id="Objectives"></a>
### <a name="objectives"></a>目标

在本动手实验，您将学习如何：

- 从服务器中将通知发送到使用 SignalR 客户端。
- SignalR 应用程序使用扩大**SQL Server**。

<a id="Prerequisites"></a>
### <a name="prerequisites"></a>系统必备

完成本动手实验需要以下：

- [Visual Studio Express 2013 for Web](https://www.microsoft.com/visualstudio/)或更高版本

<a id="Setup"></a>
### <a name="setup"></a>安装

若要运行本动手实验中练习，需要先设置你的环境。

1. 打开 Windows 资源管理器窗口并浏览到本实验**源**文件夹。
2. 右键单击**Setup.cmd** ，然后选择**以管理员身份运行**以启动将配置你的环境并安装本实验的 Visual Studio 代码段的安装过程。
3. 如果显示用户帐户控制对话框中，确认要继续执行的操作。

> [!NOTE]
> 请确保您运行安装程序之前已为此实验室的所有依赖项。


<a id="CodeSnippets"></a>
### <a name="using-the-code-snippets"></a>使用代码片段

在整个实验文档中，将指示您插入代码块。 为方便起见，此代码的大部分以 Visual Studio 代码段，可以在 Visual Studio 2013，为了避免必须手动添加访问从形式提供。

> [!NOTE]
> 每个练习均附带位于中的开始解决方案**开始**本练习，您可以按照独立于其他每个练习的文件夹。 请注意在练习期间添加的代码片段缺少这些开始解决方案中，并且可能无法工作，直到完成该练习。 在练习的源代码，您将发现**最终**包含具有无法完成相应练习中的步骤得到的代码的 Visual Studio 解决方案文件夹。 如果您在演练本动手实验需要更多帮助，可以使用这些解决方案作为指南。


* * *

<a id="Exercises"></a>
## <a name="exercises"></a>练习

本动手实验包括以下练习：

1. [使用 SignalR 的实时数据处理](#Exercise1)
2. [向外扩展使用的 SQL Server](#Exercise2)

估计的时间才能完成此实验：**60 分钟**

> [!NOTE]
> 在首次启动 Visual Studio，您必须选择一个预定义的设置集合。 每个预定义的集合旨在符合特定的开发风格，并确定窗口布局、 编辑器行为、 IntelliSense 代码段和对话框选项。 此实验中的过程描述了完成给定的任务在 Visual Studio 中使用时所需的操作**常规开发设置**集合。 如果您为您的开发环境选择不同的设置集合，可能会中应考虑到的步骤有所不同。


<a id="Exercise1"></a>
### <a name="exercise-1-working-with-real-time-data-using-signalr"></a>练习 1:使用 SignalR 的实时数据处理

尽管聊天通常用作示例，您可以为一个整体很多更多的实时 Web 功能。 每当用户刷新网页可看到新数据或页面实现 Ajax 长轮询以检索新数据，可以使用 SignalR。

SignalR 支持**服务器推送**或**广播**功能; 它会自动处理的连接管理。 在客户端-服务器通信的经典 HTTP 连接，为每个请求重新建立连接后但 SignalR 提供了客户端和服务器之间的持续性连接。 在 SignalR 中的服务器代码调用中使用远程过程调用 (RPC) 的浏览器的客户端代码，而不是请求-响应模型如今我们知道。

在此练习中，您将配置**极客测验**应用程序使用 SignalR 显示统计信息仪表板使用已更新的指标，而无需刷新整个页面。

<a id="Ex1Task1"></a>
#### <a name="task-1--exploring-the-geek-quiz-statistics-page"></a>任务 1 – 探索极客测验统计信息页

在此任务中，将通过应用程序并验证统计信息页上的显示方式，并可以如何改进信息的方式更新。

1. 打开**Visual Studio Express 2013 for Web** ，然后打开**GeekQuiz.sln**解决方案位于**Source\Ex1 WorkingWithRealTimeData\Begin**文件夹。
2. 按**F5**运行该解决方案。 **登录**页应显示在浏览器中。

    ![运行解决方案](real-time-web-applications-with-signalr/_static/image2.png "运行解决方案")

    *运行解决方案*
3. 单击**注册**右上角的页后，可以在应用程序中创建新用户。

    ![注册链接](real-time-web-applications-with-signalr/_static/image3.png "注册链接")

    *注册链接*
4. 在中**注册**页上，输入**用户名**并**密码**，然后单击**注册**。

    ![注册用户](real-time-web-applications-with-signalr/_static/image4.png "注册用户")

    *注册用户*
5. 应用程序注册新帐户和用户进行身份验证和重定向回到主页上显示的第一个测验问题。
6. 打开**统计信息**页上，在新窗口中，并将放**主页**页并**统计信息**页上的并排方案。

    ![通过并行 windows](real-time-web-applications-with-signalr/_static/image5.png "端 windows 端")

    *通过并行 windows*
7. 在中**主页**页上，通过单击某个选项来回答问题。

    ![回答问题](real-time-web-applications-with-signalr/_static/image6.png "回答问题")

    *回答问题*
8. 单击其中一个按钮后, 应显示答案。

    ![问题回答正确](real-time-web-applications-with-signalr/_static/image7.png "正确回答的问题")

    *正确回答的问题*
9. 请注意统计信息页中提供的信息已过时。 刷新页面才能看到更新后的结果。

    ![统计信息页面](real-time-web-applications-with-signalr/_static/image8.png "统计信息页")

    *统计信息页*
10. 返回到 Visual Studio 和停止调试。

<a id="Ex1Task2"></a>
#### <a name="task-2--adding-signalr-to-geek-quiz-to-show-online-charts"></a>任务 2 – 为极客测验以显示联机图表添加 SignalR

在本任务中，将添加到解决方案的 SignalR，并将更新发送到客户端自动在新的答案发送到服务器时。

1. 从**工具**在 Visual Studio 中，选择菜单**NuGet 包管理器**，然后单击**程序包管理器控制台**。
2. 在中**程序包管理器控制台**窗口中，执行以下命令：

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample1.ps1)]

    ![SignalR 包安装](real-time-web-applications-with-signalr/_static/image9.png "SignalR 包安装")

    *SignalR 包安装*

   > [!NOTE]
   > 安装时**SignalR** NuGet 包版本 2.0.2 从全新的 MVC 5 应用程序，你将需要手动更新**OWIN**程序包更新到版本 2.0.1 （或更高版本） 安装 SignalR 之前。 若要执行此操作，可以执行以下脚本**程序包管理器控制台**:
   > 
   > [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample2.ps1)]
   > 
   > 在 SignalR 的未来版本中，将自动更新 OWIN 依赖项。
3. 在中**解决方案资源管理器**，展开**脚本**文件夹，然后请注意，SignalR *js*文件已添加到解决方案。

    ![SignalR JavaScript 引用](real-time-web-applications-with-signalr/_static/image10.png "SignalR JavaScript 引用")

    *SignalR JavaScript 引用*
4. 在中**解决方案资源管理器**，右键单击**GeekQuiz**项目，选择**添加** | **新文件夹**，并将其命名**中心**。
5. 右键单击**中心**文件夹，然后选择**添加 |新项**。

    ![添加新项](real-time-web-applications-with-signalr/_static/image11.png "添加新项")

    *添加新项*
6. 在中**添加新项**对话框中，选择**Visual C# |Web |SignalR**的左窗格中，选择节点**SignalR Hub 类 (v2)** 在中心窗格中，将文件命名**StatisticsHub.cs**然后单击**添加**。

    ![添加新项对话框](real-time-web-applications-with-signalr/_static/image12.png "添加新项对话框")

    *添加新项对话框*
7. 中的代码替换**StatisticsHub**用下面的代码的类。

    (代码段- *RealTimeSignalR-Ex1-StatisticsHubClass*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample3.cs)]
8. 打开**Startup.cs** ，并在末尾添加以下行**配置**方法。

    (代码段- *RealTimeSignalR-Ex1-MapSignalR*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample4.cs)]
9. 打开**StatisticsService.cs**页内**服务**文件夹并添加以下 using 指令。

    (代码段- *RealTimeSignalR-Ex1-UsingDirectives*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample5.cs)]
10. 若要通知连接的客户端的更新，请首先检索**上下文**当前连接对象。 **中心**对象包含用于将消息发送到单个客户端或广播到所有已连接的客户端的方法。 添加以下方法**StatisticsService**类广播的统计信息数据。

    (代码段- *RealTimeSignalR-Ex1-NotifyUpdatesMethod*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample6.cs)]

    > [!NOTE]
    > 在上面的代码中，您将使用任意方法名称来在客户端上调用的函数 (即： *updateStatistics*)。 您指定的方法名称被解释为动态对象，这意味着没有 IntelliSense 或它的编译时验证。 在运行时计算表达式。 方法调用执行时，SignalR 将方法名称和参数值发送到客户端。 如果客户端相匹配的名称，调用方法和参数值传递给它的方法。 如果在客户端上不找到任何匹配的方法，则不会引发错误。 有关详细信息，请参阅[ASP.NET SignalR 中心 API 指南](../guide-to-the-api/hubs-api-guide-server.md)。
11. 打开**TriviaController.cs**页内**控制器**文件夹并添加以下 using 指令。

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample7.cs)]
12. 将以下突出显示的代码添加到**Post**操作方法。

    (代码段- *RealTimeSignalR-Ex1-NotifyUpdatesCall*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample8.cs)]
13. 打开**Statistics.cshtml**页内**视图 |主页**文件夹。 找到**脚本**部分，并在部分的开头添加以下脚本引用。

    (代码段- *RealTimeSignalR-Ex1-SignalRScriptReferences*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample9.cshtml)]

    > [!NOTE]
    > 当您将 SignalR 和其他脚本库添加到你的 Visual Studio 项目中时，包管理器可能会安装比本主题中所示的版本更新的 SignalR 脚本文件的版本。 请确保在代码中的脚本引用与脚本库在项目中安装的版本匹配。
14. 添加以下突出显示的代码连接到 SignalR 集线器的客户端并从中心接收新消息时更新统计信息数据。

    (Code Snippet - *RealTimeSignalR - Ex1 - SignalRClientCode*)

    [!code-cshtml[Main](real-time-web-applications-with-signalr/samples/sample10.cshtml)]

    在此代码中，所以在创建集线器代理并注册事件处理程序以侦听的服务器发送的消息。 在这种情况下，侦听通过发送的消息*updateStatistics*方法。

<a id="Ex1Task3"></a>
#### <a name="task-3--running-the-solution"></a>任务 3 – 运行解决方案

在本任务中，将运行解决方案，以验证使用回答新问题后 SignalR 自动更新统计信息视图。

1. 按**F5**运行该解决方案。

    > [!NOTE]
    > 如果尚未登录到应用程序，使用在任务 1 中创建的用户登录。
2. 打开**统计信息**页上，在新窗口中，并将放**主页**页并**统计信息**页上的同时，就像在任务 1 中。
3. 在中**主页**页上，通过单击某个选项来回答问题。

    ![回答另一个问题](real-time-web-applications-with-signalr/_static/image13.png "回答另一个问题")

    *回答另一个问题*
4. 单击其中一个按钮后, 应显示答案。 请注意页面上的统计信息自动更新后回答这个问题，而无需刷新整个页面的更新信息。

    ![在应答后刷新统计信息页面](real-time-web-applications-with-signalr/_static/image14.png "答案后刷新统计信息页面")

    *刷新后答案的统计信息页*

<a id="Exercise2"></a>
### <a name="exercise-2-scaling-out-using-sql-server"></a>练习 2:向外扩展使用的 SQL Server

Web 应用程序时，您通常可以之间*纵向*并*向外扩展*选项。 *纵向扩展*意味着使用更大的服务器，具有更多的资源 （CPU、 RAM、 等） 时*横向扩展*意味着添加更多服务器来处理负载。 后者的问题是，可以获取客户端路由到不同的服务器。 连接到一个服务器的客户端不会收到从另一台服务器发送的消息。

通过使用名为的组件来解决这些问题*底板*、 邮件服务器之间进行转发。 使用启用了基架，每个应用程序实例将消息发送到底板，并底板将其转发到其他应用程序实例。

目前有三种类型的 SignalR 的背板：

- **Windows Azure 服务总线**。 服务总线是允许组件将松散耦合的消息发送的消息传送基础结构。
- **SQL Server**。 SQL Server 底板将消息写入 SQL 表。 基架使用 Service Broker 为有效的消息传递。 但是，它还适用如果未启用 Service Broker。
- **Redis**。 Redis 是内存中键 / 值存储。 Redis 支持发布/订阅 （"发布/订阅"） 模式，用于发送消息。

通过消息总线发送每条消息。 消息总线实现[IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx)接口，它提供了发布/订阅抽象。 通过替换默认的工作的底板来**IMessageBus**设计为对该底板总线。

每个服务器实例连接到总线通过基架。 发送一条消息后，转到基架，并底板将其发送到每个服务器。 当服务器从底板收到一条消息时，它会将消息存储在其本地缓存中。 服务器然后将其本地缓存中的消息传送到客户端。

有关 SignalR 基架的工作原理，阅读此详细信息[一文](../performance/scaleout-in-signalr.md)。

> [!NOTE]
> 有某些情况下，其中底板可能成为瓶颈。 下面是一些典型的 SignalR 方案：
> 
> - [服务器广播](tutorial-server-broadcast-with-signalr.md)（例如，股票行情）：背板适合此方案中，因为服务器控制发送消息的速率。
> - [客户端到客户端](tutorial-getting-started-with-signalr.md)（如聊天）：在此方案中，基架可能如果成为瓶颈的消息数会随着客户端; 的数量也就是说，如果消息的速率增长按比例更多客户端加入。
> - [高频率实时功能](tutorial-high-frequency-realtime-with-signalr.md)（例如，实时游戏）：基架不建议用于此方案。


在此练习中，您将使用**SQL Server**分发跨消息**极客测验**应用程序。 若要了解如何设置配置，但是，为了获取完整的效果单一测试计算机上运行这些任务，将需要 SignalR 应用程序部署到两个或多个服务器。 其中一台服务器，或单独的专用服务器上，还必须安装 SQL Server。

![向外扩展使用 SQL Server 关系图](real-time-web-applications-with-signalr/_static/image15.png)

<a id="Ex2Task1"></a>
#### <a name="task-1---understanding-the-scenario"></a>任务 1-了解方案

在本任务中，你将运行 2 个实例的**极客测验**模拟多个 IIS 实例在本地计算机上。 在此方案中，回答琐碎内容问题上一个应用程序时，更新不会在第二个实例统计信息页上收到通知。 此模拟类似于你的应用程序多个实例部署的环境和使用负载均衡器与它们进行通信。

1. 打开**Begin.sln**解决方案位于**源/Ex2-ScalingOutWithSQLServer/开始**文件夹。 加载后，您会发现上**服务器资源管理器**解决方案有两个项目具有相同结构但不同的名称。 这将模拟在本地计算机上运行同一应用程序的两个实例。

    ![开始解决方案模拟 2 个实例的极客测验](real-time-web-applications-with-signalr/_static/image16.png "开始模拟极客测验的 2 个实例的解决方案")

    *开始模拟极客测验的 2 个实例的解决方案*
2. 打开解决方案的属性页上，右键单击解决方案节点并选择**属性**。 下**启动项目**，选择**多个启动项目**并更改**操作**到这两个项目的值*启动*。

    ![启动多个项目](real-time-web-applications-with-signalr/_static/image17.png "启动多个项目")

    *启动多个项目*
3. 按**F5**运行该解决方案。 应用程序将启动的两个实例**极客测验**中不同的端口，模拟相同的应用程序的多个实例。 将一个固定的左侧和右侧屏幕的其他浏览器。 你的凭据进行登录或注册一个新用户。 登录后，在左侧保留琐碎内容页并转到**统计信息**右侧上的浏览器中的页。

    ![极客测验并排显示](real-time-web-applications-with-signalr/_static/image18.png)

    *极客测验并排显示*

    ![在不同的端口的极客测验](real-time-web-applications-with-signalr/_static/image19.png)

    *在不同的端口的极客测验*
4. 左侧浏览器中启动回答的问题，您会注意**统计信息**不更新页面右侧的浏览器中。 这是因为**SignalR**使用本地缓存，以将消息分配到其客户端和这种情况下可以模拟多个实例，因此它们之间不共享缓存。 你可以验证**SignalR**测试相同的步骤，但使用的单个应用程序是否工作。 在以下任务将配置基架以在实例之间复制消息。
5. 返回到 Visual Studio 和停止调试。

<a id="Ex2Task2"></a>
#### <a name="task-2--creating-the-sql-server-backplane"></a>任务 2 – 创建 SQL Server 底板

在此任务中，将创建的数据库，将充当为底板**极客测验**应用程序。 将使用**SQL Server 对象资源管理器**浏览你的服务器并初始化该数据库。 此外，你将启用**Service Broker**。

1. 在中**Visual Studio**，打开菜单**视图**，然后选择**SQL Server 对象资源管理器**。
2. 通过右键单击连接到 LocalDB 实例**SQL Server**节点并选择**添加 SQL Server...** 选项。

    ![添加 SQL Server 实例](real-time-web-applications-with-signalr/_static/image20.png "添加 SQL Server 实例")

    *将 SQL Server 实例添加到 SQL Server 对象资源管理器*
3. 设置**服务器名称**到 *(localdb) \v11.0*并保留**Windows 身份验证**作为身份验证模式。 单击**Connect**以继续。

    ![连接到 LocalDB](real-time-web-applications-with-signalr/_static/image21.png "连接到 LocalDB")

    *连接到 LocalDB*
4. 现在，连接到 LocalDB 实例，需要创建将表示 SQL Server 底板 SignalR 的一个数据库。 若要执行此操作，右键单击**数据库**节点，然后选择**添加新的数据库**。

    ![添加新的数据库](real-time-web-applications-with-signalr/_static/image22.png "添加新的数据库")

    *添加新的数据库*
5. 将数据库名称设置为*SignalR*然后单击**确定**来创建它。

    ![创建 SignalR 数据库](real-time-web-applications-with-signalr/_static/image23.png "创建 SignalR 数据库")

    *创建 SignalR 数据库*

    > [!NOTE]
    > 可以选择数据库的任意名称。
6. 若要从底板更有效地接收更新，建议为数据库启用服务中介程序。 Service Broker 提供了对消息传送和队列在 SQL Server 中的本机支持。 无需 Service Broker 还正常底板。 通过右键单击该数据库并选择打开一个新查询**新查询**。

    ![打开新查询](real-time-web-applications-with-signalr/_static/image24.png "打开新查询")

    *打开新查询*
7. 若要检查是否启用 Service Broker，请查询**是\_broker\_启用**中的列**sys.databases**目录视图。 在最近打开的查询窗口中执行以下脚本。

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample11.sql)]

    ![查询服务代理状态](real-time-web-applications-with-signalr/_static/image25.png "查询服务代理状态")

    *查询服务代理状态*
8. 如果的值**是\_broker\_启用**您的数据库中的列&quot;0&quot;，使用以下命令来启用它。 替换**&lt;YOUR DATABASE&gt;** 具有创建数据库时设置的名称 (例如：SignalR)。

    [!code-sql[Main](real-time-web-applications-with-signalr/samples/sample12.sql)]

    ![启用 Service Broker](real-time-web-applications-with-signalr/_static/image26.png "启用 Service Broker")

    *启用 Service Broker*

    > [!NOTE]
    > 如果此查询出现死锁，请确保没有应用程序连接到数据库。

<a id="Ex2Task3"></a>
#### <a name="task-3--configuring-the-signalr-application"></a>任务 3 – 配置 SignalR 应用程序

在本任务中，您将配置**极客测验**连接到 SQL Server 背板。 首先，您将添加**SignalR.SqlServer** NuGet 包并设置的连接字符串到底板数据库。

1. 打开**程序包管理器控制台**从**工具** > **NuGet 包管理器**。 请确保**GeekQuiz**中选择项目**默认项目**下拉列表。 键入以下命令以安装**Microsoft.AspNet.SignalR.SqlServer** NuGet 包。

    [!code-powershell[Main](real-time-web-applications-with-signalr/samples/sample13.ps1)]
2. 对项目重复上一步，但这一次**GeekQuiz2**。
3. 若要配置 SQL Server 基架，请打开**Startup.cs**的文件**GeekQuiz**项目，然后将以下代码添加到**配置**方法。 替换**&lt;YOUR DATABASE&gt;** 与你创建 SQL Server 底板时所用的数据库名称。 重复此步骤对于**GeekQuiz2**项目。

    (代码段- *RealTimeSignalR-Ex2-StartupConfiguration*)

    [!code-csharp[Main](real-time-web-applications-with-signalr/samples/sample14.cs)]
4. 现在，这两个项目配置为使用 SQL Server 底板，按**F5**同时运行它们。
5. 同样， **Visual Studio**将启动的两个实例**极客测验**中不同的端口。 将一个浏览器固定到左侧和右侧屏幕的其他和您的凭据进行登录。 在左侧保留琐碎内容页并转到**统计信息**页面调入正确的浏览器。
6. 开始回答问题在左侧浏览器中。 这一次，**统计信息**得益于底板更新页面。 应用程序之间切换 (**统计信息**现已在左侧，并**琐碎内容**位于右侧) 和重复执行测试以验证它是否正常工作的两个实例。 底板充当*共享缓存*的消息的每个连接的服务器，且每个服务器会将消息存储在其自己的本地缓存要分发到连接的客户端中。
7. 返回到 Visual Studio 和停止调试。
8. SQL Server 底板组件会自动生成上指定的数据库所需的表。 在中**SQL Server 对象资源管理器**面板中，打开为基架创建的数据库 (例如：SignalR) 展开其表。 你应该会看到以下表：

    ![基架生成的表](real-time-web-applications-with-signalr/_static/image27.png)

    *基架生成的表*
9. 右键单击**SignalR.Messages\_0**表，然后选择**查看数据**。

    ![查看 SignalR 基架消息表](real-time-web-applications-with-signalr/_static/image28.png)

    *查看 SignalR 基架消息表*
10. 可以看到不同的消息发送到**中心**回答琐碎内容问题时。 底板这些将消息分发到已连接的任何实例。

    ![底板消息表](real-time-web-applications-with-signalr/_static/image29.png)

    *底板消息表*

* * *

<a id="Summary"></a>
## <a name="summary"></a>总结

在本动手实验，您已了解如何添加**SignalR**在应用程序和发送通知从服务器到使用您连接的客户端**中心**。 此外，您学习了如何通过使用横向扩展你的应用程序*底板*组件时你的应用程序部署在多个 IIS 实例。
