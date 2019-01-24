---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 教程：与 SignalR 2 实时聊天 |Microsoft Docs
author: bradygaster
description: 本教程介绍如何使用 SignalR 创建实时聊天应用程序。 将 SignalR 添加到空的 ASP.NET web 应用程序。
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 90f2c03fbda522e3a46200bc0132cc74100ce70f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836787"
---
# <a name="tutorial-real-time-chat-with-signalr-2"></a>教程：与 SignalR 2 实时聊天

本教程演示如何使用 SignalR 创建实时聊天应用程序。 将 SignalR 添加到空的 ASP.NET web 应用程序和创建 HTML 页以发送和显示的消息。

在本教程中，你将了解：

> [!div class="checklist"]
> * 设置项目
> * 运行示例
> * 检查代码

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a>系统必备

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。

## <a name="set-up-the-project"></a>设置项目

本部分演示如何使用 Visual Studio 2017 和 SignalR 2 来创建空的 ASP.NET web 应用程序，将 SignalR 中，并创建聊天应用程序。

1. 在 Visual Studio 中创建 ASP.NET Web 应用程序。

    ![创建 web](tutorial-getting-started-with-signalr/_static/image2.png)

1. 在中**新建 ASP.NET 项目-SignalRChat**窗口中，保留**空**，并选择**确定**。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-SignalRChat**，选择**已安装** > **Visual C#**   >  **Web**  > **SignalR** ，然后选择**SignalR Hub 类 (v2)**。

1. 将类命名*ChatHub*并将其添加到项目。

    此步骤将创建*ChatHub.cs*类文件并添加一组脚本文件和 SignalR 支持到项目的程序集引用。

1. 在新的代码替换为*ChatHub.cs*类文件，此代码：

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-SignalRChat**选择**已安装** > **Visual C#**   >  **Web** ，然后选择**OWIN 启动类**。

1. 将类命名*启动*并将其添加到项目。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **HTML 页**。

1. 命名新页面*索引*，然后选择**确定**。

1. 在中**解决方案资源管理器**，右键单击你创建的 HTML 页面，然后选择**设为起始页**。

1. HTML 页中的默认代码替换此代码：

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. 在中**解决方案资源管理器**，展开**脚本**。

    适用于 jQuery 和 SignalR 的脚本库将显示在该项目。

    > [!IMPORTANT]
    > 包管理器可能已安装的 SignalR 脚本的更高版本。

1. 检查与项目中的脚本文件的版本相对应的代码块中的脚本引用。

    从原始的代码块的脚本引用：
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. 如果不匹配，更新 *.html*文件。

1. 从菜单栏中，选择**文件** > **全部保存**。

## <a name="run-the-sample"></a>运行示例

1. 在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行示例。

    ![输入用户名](tutorial-getting-started-with-signalr/_static/image3.png)

1. 在浏览器打开时，输入你的聊天身份的名称。

1. 从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。

1. 在每个浏览器中，输入唯一名称。

1. 现在，添加注释并选择**发送**。 在其他浏览器中重复的。 注释将显示在实时中。

    > [!NOTE]
    > 此简单的聊天应用程序不维护服务器上的讨论上下文。 在中心广播到所有当前用户的注释。 加入聊天更高版本的用户将看到消息从时添加它们加入。

    请参阅聊天应用程序在三个不同的浏览器中的运行方式。 当 Tom、 Anand 和 Susan 发送消息时，所有浏览器实时更新：

    ![所有三个浏览器显示相同的聊天历史记录](tutorial-getting-started-with-signalr/_static/image4.png)

1. 在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。 没有名为的脚本文件*中心*SignalR 库生成在运行时。 此文件管理的 jQuery 脚本和服务器端代码之间的通信。

    ![在脚本文档节点中自动生成中心脚本](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a>检查代码

SignalRChat 应用程序演示了两个基本的 SignalR 开发任务。 它显示了如何创建一个中心。 服务器使用的主要协调对象为该中心。 在中心使用 SignalR jQuery 库来发送和接收消息。

### <a name="signalr-hubs-in-the-chathubcs"></a>SignalR 集线器中 ChatHub.cs

在上面的代码示例`ChatHub`类派生自`Microsoft.AspNet.SignalR.Hub`类。 派生自`Hub`类是一种有用的方式来构建 SignalR 应用程序。 可以在中心类上创建的公共方法，然后通过调用从网页中的脚本中使用这些方法。

在聊天代码中，客户端调用`ChatHub.Send`方法发送一封新邮件。 在中心然后将消息发送到所有客户端，通过调用`Clients.All.broadcastMessage`。

`Send`方法演示了多个中心概念：

* 集线器上声明的公共方法，以便客户端可以调用它们。

* 使用`Microsoft.AspNet.SignalR.Hub.Clients`与所有客户端通信的动态属性连接到此中心。

* 在客户端上调用的函数 (如`broadcastMessage`函数) 来更新客户端。

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a>SignalR 和 jQuery 在 index.html 中

*Index.html*页中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。 代码执行许多重要任务。 它声明的代理服务器能够引用中心，声明了函数内容推送到客户端，可以调用在服务器和它的起始位置将消息发送到中心的连接。

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> 在 JavaScript 中于服务器类及其成员的引用必须是驼峰式大小写。 代码示例引用C# *ChatHub*中作为 JavaScript 类`chatHub`。

在此代码块中，创建脚本中的回调函数。

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。 两个行，进行 HTML 编码的内容之前显示它是可选的显示阻止脚本注入的好方法。

此代码打开到中心的连接。

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> 此方法可确保代码建立的连接，事件处理程序在执行之前。

代码启动连接，然后将其传递函数来处理单击事件上**发送**HTML 页中的按钮。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

## <a name="additional-resources"></a>其他资源

有关 SignalR 的详细信息，请参阅以下资源：

* [SignalR 项目](http://signalr.net)

* [SignalR Github 和示例](https://github.com/SignalR/SignalR)

* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>后续步骤

在本教程中你：

> [!div class="checklist"]
> * 设置项目
> * 运行示例
> * 检查代码

转到下一步的文章，了解如何使用 SignalR 和 MVC 5。
> [!div class="nextstepaction"]
> [SignalR 2 和 MVC 5](tutorial-getting-started-with-signalr-and-mvc.md)