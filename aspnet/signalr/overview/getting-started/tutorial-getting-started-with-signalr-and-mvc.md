---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 教程：使用 SignalR 2 和 MVC 5 的实时聊天 |Microsoft Docs
author: bradygaster
description: 本教程演示如何使用 ASP.NET SignalR 2 来创建实时聊天应用程序。 将 SignalR 添加到 MVC 5 应用程序。
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1b02aecc68a93dbd6373ca5304530e76c9d0b6b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836995"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a>教程：使用 SignalR 2 和 MVC 5 的实时聊天

本教程演示如何使用 ASP.NET SignalR 2 来创建实时聊天应用程序。 将 SignalR 添加到 MVC 5 应用程序并创建聊天视图以发送和显示的消息。

在本教程中，你将了解：

> [!div class="checklist"]
> * 设置项目
> * 运行示例
> * 检查代码

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>系统必备

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。

## <a name="set-up-the-project"></a>设置项目

本部分演示如何使用 Visual Studio 2017 和 SignalR 2 来创建一个空的 ASP.NET MVC 5 应用程序、 添加 SignalR 库，并创建聊天应用程序。

1. 在 Visual Studio 中，创建面向.NET Framework 4.5 的 C# ASP.NET 应用程序、 其命名为 SignalRChat，并单击确定。

    ![创建 web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. 在中**新 ASP.NET Web 应用程序-SignalRMvcChat**，选择**MVC** ，然后选择**更改身份验证**。

1. 在中**更改身份验证**，选择**无身份验证**然后单击**确定**。

    ![选择无身份验证](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. 在中**新 ASP.NET Web 应用程序-SignalRMvcChat**，选择**确定**。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-SignalRChat**，选择**已安装** > **Visual C#**   >  **Web**  > **SignalR** ，然后选择**SignalR Hub 类 (v2)**。

1. 将类命名*ChatHub*并将其添加到项目。

    此步骤将创建*ChatHub.cs*类文件并添加一组脚本文件和 SignalR 支持到项目的程序集引用。

1. 在新的代码替换为*ChatHub.cs*类文件，此代码：

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **类**。

1. 将新类命名*启动*并将其添加到项目。

1. 中的代码替换*Startup.cs*类文件，此代码：

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. 在中**解决方案资源管理器**，选择**控制器** > **HomeController.cs**。

1. 将以下方法添加到*HomeController.cs*。

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    此方法返回**聊天**在稍后的步骤中创建的视图。

1. 在中**解决方案资源管理器**，右键单击**视图** > **主页**，然后选择**添加** >   **视图**。

1. 在中**添加视图**，将新视图命名**聊天**，然后选择**添加**。

1. 内容替换为**Chat.cshtml**使用以下代码：

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. 在中**解决方案资源管理器**，展开**脚本**。

    适用于 jQuery 和 SignalR 的脚本库将显示在该项目。

    > [!IMPORTANT]
    > 包管理器可能已安装的 SignalR 脚本的更高版本。

1. 检查与项目中的脚本文件的版本相对应的代码块中的脚本引用。

    从原始的代码块的脚本引用：

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. 如果不匹配，更新 *.cshtml*文件。

1. 从菜单栏中，选择**文件** > **全部保存**。

## <a name="run-the-sample"></a>运行示例

1. 在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行示例。

    ![输入用户名](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. 在浏览器打开时，输入你的聊天身份的名称。

1. 从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。

1. 在每个浏览器中，输入唯一名称。

1. 现在，添加注释并选择**发送**。 在其他浏览器中重复的。 注释将显示在真实时间中。

    > [!NOTE]
    > 此简单的聊天应用程序不维护服务器上的讨论上下文。 在中心广播到所有当前用户的注释。 加入聊天更高版本的用户将看到消息从时添加它们加入。

    请参阅聊天应用程序在三个不同的浏览器中的运行方式。 当 Tom、 Anand 和 Susan 发送消息时，所有浏览器实时更新：

    ![所有三个浏览器显示相同的聊天历史记录](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. 在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。 没有名为的脚本文件*中心*SignalR 库生成在运行时。 此文件管理的 jQuery 脚本和服务器端代码之间的通信。

    ![在脚本文档节点中自动生成中心脚本](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a>检查代码

SignalR 聊天应用程序演示了两个基本的 SignalR 开发任务。 它显示了如何创建一个中心。 服务器使用的主要协调对象为该中心。 在中心使用 SignalR jQuery 库来发送和接收消息。

### <a name="signalr-hubs-in-the-chathubcs"></a>SignalR 集线器中 ChatHub.cs

在代码示例中，`ChatHub`类派生自`Microsoft.AspNet.SignalR.Hub`类。 派生自`Hub`类是一种有用的方式来构建 SignalR 应用程序。 可以在中心类上创建的公共方法，然后通过调用从网页中的脚本中访问这些方法。

在聊天代码中，客户端调用`ChatHub.Send`方法发送一封新邮件。 在中心反过来将消息发送给所有客户端，通过调用`Clients.All.addNewMessageToPage`。

`Send`方法演示了多个中心概念：

* 集线器上声明的公共方法，以便客户端可以调用它们。

* 使用`Microsoft.AspNet.SignalR.Hub.Clients`与所有客户端通信的动态属性连接到此中心。

* 在客户端上调用的函数 (如`addNewMessageToPage`函数) 来更新客户端。

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a>SignalR 和 jQuery Chat.cshtml

*Chat.cshtml*视图文件中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。  代码执行许多重要任务。 它创建为中心的自动生成代理的引用，声明一个函数可以调用服务器可将内容推送到客户端，并开始将消息发送到中心的连接。

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> 在 JavaScript 中，于服务器类及其成员的引用位于驼峰式大小写。 代码示例引用C#`ChatHub`中作为 JavaScript 类`chatHub`。

在此代码块中，创建脚本中的回调函数。

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。 可选调用`htmlEncode`函数将显示一种方式，向 HTML 页中显示之前编码消息内容。 它是一种方法，以防止脚本注入。

此代码打开到中心的连接。

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> 此方法可确保事件处理程序在执行之前建立的连接。

代码启动连接，然后将其传递函数来处理单击事件上**发送**聊天页中的按钮。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

## <a name="additional-resources"></a>其他资源

有关 SignalR 的详细信息，请参阅以下资源：

* [SignalR 项目](http://signalr.net)

* [SignalR GitHub 和示例](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 设置项目
> * 运行示例
> * 检查代码

转到下一步的文章，了解如何创建使用 ASP.NET SignalR 2 提供高频率的消息传送功能的 web 应用程序。
> [!div class="nextstepaction"]
> [使用高频率消息传送的 web 应用](tutorial-high-frequency-realtime-with-signalr.md)