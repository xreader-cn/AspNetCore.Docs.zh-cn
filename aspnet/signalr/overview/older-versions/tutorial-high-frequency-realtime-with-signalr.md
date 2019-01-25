---
uid: signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
title: 高频率实时功能与 SignalR 1.x |Microsoft Docs
author: bradygaster
description: 本教程演示如何创建使用 ASP.NET SignalR 来提供高频率消息传送功能的 web 应用程序。 高频率内消息传送...
ms.author: bradyg
ms.date: 04/16/2013
ms.assetid: ad2a5da5-2e79-40ea-bc84-028d327f5982
msc.legacyurl: /signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 6df35a420a0733003808a12d065b03f08ef56dd9
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837918"
---
<a name="high-frequency-realtime-with-signalr-1x"></a>高频率实时功能与 SignalR 1.x
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本教程演示如何创建使用 ASP.NET SignalR 来提供高频率消息传送功能的 web 应用程序。 在这种情况下消息传送高频率意味着费率是固定的; 在发送的更新对于此应用程序，最多 10 个消息的第二个。
> 
> 将在本教程中创建的应用程序显示用户可以将一个形状。 在所有其他连接的浏览器中的形状位置然后将更新以匹配使用定时的更新拖动形状的位置。
> 
> 本教程中介绍的概念有实时游戏中的应用程序和其他模拟应用程序。
> 
> 在本教程的注释是欢迎使用。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com)。


## <a name="overview"></a>概述

本教程演示如何创建应用程序与其他浏览器实时共享对象的状态。 我们将创建应用程序被称为 MoveShape。 MoveShape 页将显示的 HTML Div 元素，用户可拖动;当用户拖动该 Div，其新位置将发送到服务器，然后告知所有其他连接的客户端以更新要匹配的形状的位置。

![应用程序窗口](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

在本教程中创建的应用程序基于 Damian edwards 提供演示。 可以看到其中包含此演示视频[此处](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR)。

本教程将首先演示如何将 SignalR 消息发送时所触发该形状拖动每个事件。 每个连接的客户端然后将更新该形状的本地版本的位置每次收到一条消息。

虽然应用程序将正常使用此方法，这并不建议的编程模型，因为会有获取发送，因此客户端和服务器无法获取过重消息并将会降低性能的消息数没有上限. 在客户端上显示的动画也是不相互连接，每个方法会立即移动形状，而不是移动顺利地为每个新的位置。 本教程的后面部分将演示如何创建将由客户端或服务器，发送消息的最大速率限制的计时器函数以及如何位置之间顺利移动形状。 在本教程中创建的应用程序的最终版本可以从下载[代码库](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6)。

本教程包含以下各节：

- [系统必备](#prerequisites)
- [创建项目](#createtheproject)
- [添加 ASP.NET SignalR 和 JQuery.UI NuGet 包](#nugetpackages)
- [创建基本应用程序](#baseapp)
- [添加客户端循环](#clientloop)
- [添加服务器循环](#serverloop)
- [添加客户端上流畅的动画](#animation)
- [执行进一步的步骤](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>系统必备

本教程需要 Visual Studio 2012 或 Visual Studio 2010。 如果使用 Visual Studio 2010，则项目将使用.NET Framework 4 而不是.NET Framework 4.5。

如果正在使用 Visual Studio 2012，建议你安装[ASP.NET 和 Web Tools 2012.2 更新](https://go.microsoft.com/fwlink/?LinkId=282650)。 此更新包含新功能，如发布、 新功能的增强功能和新的模板。

如果你有 Visual Studio 2010，请确保[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安装。

<a id="createtheproject"></a>

## <a name="create-the-project"></a>创建项目

在本部分中，我们将创建 Visual Studio 中的项目。

1. 从**文件**菜单上，单击**新项目**。
2. 在中**新的项目**对话框框中，展开**C#** 下**模板**，然后选择**Web**。
3. 选择**ASP.NET 空 Web 应用程序**模板，将项目命名*MoveShapeDemo*，然后单击**确定**。

    ![创建新项目](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-and-jqueryui-nuget-packages"></a>添加的 SignalR 和 JQuery.UI NuGet 包

通过安装 NuGet 包，可以将 SignalR 功能添加到项目。 本教程还将允许该形状以拖动，经过动画处理的使用 JQuery.UI 包。

1. 单击**工具 |NuGet 包管理器 |包管理器控制台**。
2. 在包管理器中输入以下命令。

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.ps1)]

    SignalR 包作为依赖项安装其他 NuGet 包的数。 完成安装后必须全部所需的 ASP.NET 应用程序中使用 SignalR 的服务器和客户端组件。
3. 在包管理器控制台安装 JQuery 和 JQuery.UI 包输入以下命令。

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.ps1)]

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>创建基本应用程序

在本部分中，我们将创建在每个鼠标移动事件向服务器发送形状的位置的浏览器应用程序。 服务器然后会接收广播到所有其他连接的客户端此信息。 我们将在稍后的部分此应用程序上进行扩展。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加**，**类...**.将类命名**MoveShapeHub**然后单击**添加**。
2. 在新的代码替换为**MoveShapeHub**用下面的代码的类。

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.cs)]

    `MoveShapeHub`上面类是 SignalR 集线器的实现。 在中作为[SignalR 入门](index.md)教程中，中心都有客户端将直接调用的方法。 在这种情况下，客户端将发送一个对象，包含新的服务器，然后获取将广播到所有其他连接的客户端到形状的 X 和 Y 坐标。 SignalR 将自动序列化此对象使用 JSON。

    将发送到客户端的对象 (`ShapeModel`) 包含成员来存储该形状的位置。 在服务器上对象的版本还包含成员来跟踪哪些客户端的数据存储，以便指定客户端将不会发送自己的数据。 此成员使用`JsonIgnore`属性以防止进行序列化并发送到客户端。
3. 接下来，我们将设置中心应用程序启动时。 在中**解决方案资源管理器**，右键单击项目，然后单击**添加 |全局应用程序类**。 接受默认名称*Global*然后单击**确定**。

    ![添加全局应用程序类](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
4. 添加以下`using`之后所提供的语句**使用**Global.asax.cs 类中的语句。

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.cs)]
5. 添加以下行中的代码`Application_Start`要注册的默认路由 SignalR 的全局类的方法。

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    Global.asax 文件应如下所示：

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.cs)]
6. 接下来，我们将添加客户端。 在中**解决方案资源管理器**，右键单击项目，然后单击**添加 |新项**。 在中**添加新项**对话框中，选择**Html 页**。 为页面提供适当的名称 (如**Default.html**)，单击**添加**。
7. 在中**解决方案资源管理器**，右键单击刚创建的页，然后单击**设为起始页**。
8. HTML 页中的默认代码替换为以下代码片段。

    > [!NOTE]
    > 验证该脚本引用以下匹配项添加到你的项目的 Scripts 文件夹中的包。 在 Visual Studio 2010 中，JQuery 和 SignalR 添加到项目的版本可能与下面的版本编号不匹配。

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample7.html)]

    上面的 HTML 和 JavaScript 代码创建名为形状的红色 Div、 启用形状的拖放行为使用 jQuery 库，并使用形状的`drag`事件以向服务器发送形状的位置。
9. 按 F5 启动应用程序。 复制页面的 URL，并将其粘贴到第二个浏览器窗口。 在一个浏览器窗口中; 将形状在其他浏览器窗口中的形状应移动。

    ![应用程序窗口](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>添加客户端循环

由于发送的每个鼠标移动事件中的形状的位置将创建不必要数量的网络流量，从客户端的消息需要受到限制。 我们将使用 javascript`setInterval`函数来设置一个循环，将新的位置信息发送到服务器以固定费率。 此循环是"游戏循环"，驱动器的所有功能的游戏或其他模拟重复调用函数的非常基本表示形式。

1. 更新要匹配下面的代码段的 HTML 页面中的客户端代码。

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample8.html)]

    更高版本的更新添加了`updateServerModel`函数，它获取名为按固定的频率。 此函数将位置数据发送到服务器时`moved`标志指示是要发送的新位置数据。
2. 按 F5 启动应用程序。 复制页面的 URL，并将其粘贴到第二个浏览器窗口。 在一个浏览器窗口中; 将形状在其他浏览器窗口中的形状应移动。 获取发送到服务器的消息数会受到限制，因为动画不会顺利地如前一部分中所示。

    ![应用程序窗口](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>添加服务器循环

在当前应用程序，从服务器发送到客户端的消息转通常它们被接收。 这会产生类似的问题，因为客户端; 上出现过通常需要它们，然后连接可能会变得来路因此可以将消息发送。 本部分介绍如何更新服务器以实现一个计时器，用于限制的传出消息的速率。

1. 内容替换为`MoveShapeHub.cs`使用以下代码片段。

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample9.cs)]

    上面的代码中扩展客户端添加`Broadcaster`类，该类将限制传出消息使用`Timer`从.NET framework 类。

    由于中心本身是暂时性 （创建每次在需要时），`Broadcaster`将创建为单一实例。 （在.NET 4 中引入） 的迟缓初始化用于将其创建推迟，直到有必要，确保计时器启动之前完全创建第一个集线器实例。

    对客户端的调用`UpdateShape`函数然后移出该中心的`UpdateModel`方法，因此它不再接收传入消息时，立即调用。 而是发送到客户端的消息将发送的每秒 25 调用速率受`_broadcastLoop`中的计时器`Broadcaster`类。

    最后，而不是调用客户端方法从中心直接`Broadcaster`类需要获取对当前正在运行的中心的引用 (`_hubContext`) 使用`GlobalHost`。
2. 按 F5 启动应用程序。 复制页面的 URL，并将其粘贴到第二个浏览器窗口。 在一个浏览器窗口中; 将形状在其他浏览器窗口中的形状应移动。 不会在浏览器中从上一节中看到的差异，但将被发送到客户端的消息数。

    ![应用程序窗口](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>添加客户端上流畅的动画

应用程序几乎已完成，但我们可以进行一更多项改进，在客户端上形状的动态移动以响应服务器消息。 而不是将该形状的位置设置为给定服务器的新位置，我们将使用 JQuery UI 库`animate`函数以其当前和新位置之间顺利移动形状。

1. 更新客户端的`updateShape`方法看起来像下面突出显示的代码：

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample10.html?highlight=35-42)]

    上面的代码将从旧位置形状移到新服务器 （在本例中为 100 毫秒） 的动画时间间隔内提供。 新动画开始之前清除该形状上运行任何前一个动画。
2. 按 F5 启动应用程序。 复制页面的 URL，并将其粘贴到第二个浏览器窗口。 在一个浏览器窗口中; 将形状在其他浏览器窗口中的形状应移动。 在其他窗口中的形状的移动应显示较低即使如及其移动内插时间，而无需一次设置每个传入消息。

    ![应用程序窗口](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>执行进一步的步骤

在本教程中，已学习了如何发送客户端和服务器之间的高频率消息 SignalR 应用程序进行编程。 这种通信模式可用于开发联机游戏和其他模拟，如[ShootR 游戏使用 SignalR 创建](http://shootr.signalr.net)。

在本教程中创建的完整应用程序可以从下载[代码库](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6)。

若要了解有关 SignalR 开发概念的详细信息，请访问以下站点 SignalR 源代码和资源：

- [SignalR 项目](http://signalr.net)
- [SignalR Github 和示例](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
