---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 教程：使用 SignalR 2 创建高频率实时应用程序 |Microsoft Docs
author: bradygaster
description: 本教程演示如何创建使用 ASP.NET SignalR 来提供高频率消息传送功能的 web 应用程序。
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 44aaa2b0c059de310e963f642fa56c2f00a7e443
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836722"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a>教程：使用 SignalR 2 创建高频率实时应用

本教程演示如何创建使用 ASP.NET SignalR 2 提供高频率的消息传送功能的 web 应用程序。 在这种情况下，"高频率消息传送"意味着服务器将更新发送以固定费率。 发送第二个最多 10 个消息。

您创建的应用程序显示用户可以将一个形状。 服务器将更新该形状的所有连接的浏览器，以便与使用定时的更新拖动形状的位置中的位置。

本教程中介绍的概念有实时游戏中的应用程序和其他模拟应用程序。

在本教程中，你将了解：

> [!div class="checklist"]
> * 设置项目
> * 创建基本应用程序
> * 当应用启动时将映射到中心
> * 添加客户端
> * 运行应用
> * 添加客户端循环
> * 添加服务器循环
> * 添加流畅的动画

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>系统必备

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。

## <a name="set-up-the-project"></a>设置项目

在本部分中，您将创建 Visual Studio 2017 中的项目。

本部分演示如何使用 Visual Studio 2017 创建空的 ASP.NET Web 应用程序并将添加的 SignalR 和 jQuery.UI 库。

1. 在 Visual Studio 中创建 ASP.NET Web 应用程序。

    ![创建 web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. 在中**新 ASP.NET Web 应用程序-MoveShapeDemo**窗口中，保留**空**，并选择**确定**。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-MoveShapeDemo**，选择**已安装** > **Visual C#**   >  **Web**  > **SignalR** ，然后选择**SignalR Hub 类 (v2)**。

1. 将类命名*MoveShapeHub*并将其添加到项目。

    此步骤将创建*MoveShapeHub.cs*类文件。 同时，它将添加一组脚本文件和 SignalR 支持到项目的程序集引用。

1. 选择**工具** > **NuGet 包管理器** > **程序包管理器控制台**。

1. 在中**程序包管理器控制台**，运行以下命令：

    ```console
    Install-Package jQuery.UI.Combined
    ```

    该命令将安装 jQuery UI 库。 您可以使用它来对形状进行动画处理。

1. 在中**解决方案资源管理器**，展开脚本节点。

    ![脚本库引用](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    JQuery、 jQueryUI，和 SignalR 的脚本库将显示在该项目。

## <a name="create-the-base-application"></a>创建基本应用程序

在本部分中，您将创建浏览器应用程序。 应用程序在每个鼠标移动事件向服务器发送形状的位置。 服务器会广播到所有其他连接的客户端在真实时间中的此信息。 您了解有关此应用程序中后面部分的详细信息。

1. 打开*MoveShapeHub.cs*文件。

1. 中的代码替换*MoveShapeHub.cs*文件使用以下代码：

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. 保存该文件。

`MoveShapeHub`类是实现 SignalR hub。 在中作为[SignalR 入门](tutorial-getting-started-with-signalr.md)教程中，中心都有客户端直接调用的方法。 在此情况下，客户端发送的对象使用新的 X 和 Y 坐标到服务器的形状。 这些坐标获取将广播到所有其他连接的客户端。 SignalR 自动序列化此对象使用 JSON。

应用程序发送`ShapeModel`到客户端对象。 它具有成员来存储该形状的位置。 在服务器上对象的版本也有一个成员来跟踪哪些客户端的数据存储。 此对象可阻止服务器发送回发到自身的客户端的数据。 此成员使用`JsonIgnore`属性以防止序列化数据并将其发送回客户端应用程序。

## <a name="map-to-the-hub-when-app-starts"></a>当应用启动时将映射到中心

接下来，您设置映射到中心应用程序启动时。 在 SignalR 2 中，添加 OWIN 启动类创建的映射。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-MoveShapeDemo**选择**已安装** > **Visual C#**   >  **Web** ，然后选择**OWIN 启动类**。

1. 将类命名*启动*，然后选择**确定**。

1. 中的默认代码替换*Startup.cs*文件使用以下代码：

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

OWIN 启动类调用`MapSignalR`当应用程序执行`Configuration`方法。 该应用将添加到 OWIN 的启动类处理使用`OwinStartup`程序集属性。

## <a name="add-the-client"></a>添加客户端

添加客户端 HTML 页。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **HTML 页**。

1. 将该页命名为**默认**，然后选择**确定**。

1. 在中**解决方案资源管理器**，右键单击*Default.html* ，然后选择**设为起始页**。

1. 中的默认代码替换*Default.html*文件使用以下代码：

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. 在中**解决方案资源管理器**，展开**脚本**。

    适用于 jQuery 和 SignalR 的脚本库将显示在该项目。

    > [!IMPORTANT]
    > 包管理器安装 SignalR 脚本的更高版本。

1. 更新要与项目中的脚本文件的版本相对应的代码块中的脚本引用。

此 HTML 和 JavaScript 代码将创建一个红色`div`调用`shape`。 启用使用 jQuery 库的形状的拖放行为，并使用`drag`事件以向服务器发送形状的位置。

## <a name="run-the-app"></a>运行应用

你可以运行该应用到 se'e 它起作用。 当浏览器窗口中将形状时，形状将在其他浏览器过。

1. 在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行应用程序。

    ![启用调试模式下和选择播放的用户的屏幕截图。](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    使用右上角中的红色形状会打开一个浏览器窗口。

1. 复制页面的 URL。

1. 打开另一个浏览器并将 URL 粘贴到地址栏。

1. 将一个浏览器窗口中的形状。 遵循其他浏览器窗口中的形状。

尽管应用程序使用此方法的函数，它不是建议的编程模型。 获取发送的消息数没有上限。 因此，客户端和服务器获取过重消息和性能下降。 此外，应用程序在客户端上显示不相互连接的动画。 形状的每个方法，从而立即移动会发生此显得很不稳定的动画。 如果该形状将顺利地移动到每个新的位置，则是更好。 接下来，您将了解如何解决这些问题。

## <a name="add-the-client-loop"></a>添加客户端循环

发送形状上每个鼠标移动事件的位置创建不必要的网络流量大小。 应用需要来限制从客户端的消息。

使用 javascript`setInterval`函数来设置一个循环，将新的位置信息发送到服务器以固定费率。 此循环是基本表示形式的"游戏循环"。 它是游戏的重复调用的函数的驱动器的所有功能。

1. 中的客户端代码替换*Default.html*文件使用以下代码：

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > 您必须再次将脚本引用。 它们必须匹配版本的项目中的脚本。

    此新代码将添加`updateServerModel`函数。 固定频率获取调用它。 该函数将位置数据发送到服务器时`moved`标志指示是要发送的新位置数据。

1. 选择播放按钮启动应用程序

1. 复制页面的 URL。

1. 打开另一个浏览器并将 URL 粘贴到地址栏。

1. 将一个浏览器窗口中的形状。 遵循其他浏览器窗口中的形状。

由于应用程序限制到服务器，动画将不会出现顺利地获取发送的消息数未在第一个。

## <a name="add-the-server-loop"></a>添加服务器循环

在当前应用程序，从服务器发送到客户端的消息转通常在接收到。 正如我们看到客户端上，此网络流量提供了类似的问题。

应用程序可以不是在需要更频繁地发送消息。 连接可能会变得来路作为结果。 本部分介绍如何更新服务器，以添加一个计时器，用于限制的传出消息的速率。

1. 内容替换为`MoveShapeHub.cs`使用以下代码：

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. 选择播放按钮启动应用程序。

1. 复制页面的 URL。

1. 打开另一个浏览器并将 URL 粘贴到地址栏。

1. 将一个浏览器窗口中的形状。

此代码扩展客户端添加`Broadcaster`类。 新类将限制传出消息使用`Timer`从.NET framework 类。

若要了解中心本身是暂时性适合。 每次在需要时，将创建它。 因此，应用程序创建`Broadcaster`作为单一实例。 它使用延迟初始化来延迟`Broadcaster`的需要时创建。 这样可确保，应用程序之前已创建的第一个集线器实例完全启动计时器。

对客户端的调用`UpdateShape`函数然后移出该中心的`UpdateModel`方法。 不能再调用应用程序将收到传入消息时，立即。 相反，应用程序将消息发送到客户端每秒 25 调用的速率。 该过程由`_broadcastLoop`中的计时器`Broadcaster`类。

最后，而不是调用客户端方法从中心直接`Broadcaster`类需要获取对当前操作的引用`_hubContext`中心。 获取与引用`GlobalHost`。

## <a name="add-smooth-animation"></a>添加流畅的动画

应用程序几乎已完成，但我们可以进行更多的一项改进。 应用程序服务器的消息响应客户端上移动形状。 而不是将该形状的位置设置为给定服务器的新位置，使用 JQuery UI 库`animate`函数。 它可以其当前和新位置之间顺利移动形状。

1. 更新客户端`updateShape`中的方法*Default.html*文件看起来像突出显示的代码：

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. 选择播放按钮启动应用程序。

1. 复制页面的 URL。

1. 打开另一个浏览器并将 URL 粘贴到地址栏。

1. 将一个浏览器窗口中的形状。

在其他窗口中的形状的移动显示较低显得很不稳定。 应用随时间而不是每个传入消息一次设置内插及其移动。

此代码将从旧位置形状移到新。 服务器可以提供动画时间间隔内的形状的位置。 在这种情况下，这是 100 毫秒。 应用程序清除新动画开始之前，该形状上运行任何前一个动画。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

## <a name="additional-resources"></a>其他资源

你刚刚了解了有关的通信模式可用于开发联机游戏和其他模拟，如[ShootR 游戏使用 SignalR 创建](https://shootr.azurewebsites.net/)。

有关 SignalR 的详细信息，请参阅以下资源：

* [SignalR 项目](http://signalr.net)

* [SignalR GitHub 和示例](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 设置项目
> * 创建基本应用程序
> * 应用程序启动时，映射到中心
> * 添加客户端
> * 运行应用程序
> * 添加客户端循环
> * 添加服务器循环
> * 添加了流畅的动画

转到下一步的文章，了解如何创建使用 ASP.NET SignalR 2 来提供服务器广播的功能的 web 应用程序。
> [!div class="nextstepaction"]
> [SignalR 2 和服务器广播](tutorial-server-broadcast-with-signalr.md)