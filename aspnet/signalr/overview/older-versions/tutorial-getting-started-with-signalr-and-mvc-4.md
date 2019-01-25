---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
title: 教程：开始使用 SignalR 1.x 和 MVC 4 |Microsoft Docs
author: bradygaster
description: 使用 ASP.NET SignalR 和 ASP.NET MVC 4 构建实时聊天应用程序。
ms.author: bradyg
ms.date: 03/29/2013
ms.assetid: eeef9f73-6de3-49f9-b50b-9af22108f2ce
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr-and-mvc-4
msc.type: authoredcontent
ms.openlocfilehash: dd55ca22004b7e3899f6a8789494c842b984787f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837788"
---
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a>教程：开始使用 SignalR 1.x 和 MVC 4
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本教程演示如何使用 ASP.NET SignalR 创建实时聊天应用程序。 将将 SignalR 添加到 MVC 4 应用程序，并创建要发送，并显示消息的聊天视图。


## <a name="overview"></a>概述

本教程向您介绍使用 ASP.NET SignalR 和 ASP.NET MVC 4 的实时 web 应用程序开发。 本教程使用相同的聊天应用程序代码作为[SignalR 入门教程](tutorial-getting-started-with-signalr.md)，但显示了如何将其添加到 MVC 4 应用程序在基于 Internet 的模板。

在本主题中，您将学习以下的 SignalR 开发任务：

- SignalR 库添加到 MVC 4 应用程序。
- 创建用于将内容推送到客户端的中心类。
- 使用 SignalR jQuery 库在网页上发送消息并显示从中心的更新。

以下屏幕截图显示在浏览器中运行的已完成的聊天应用程序。

![聊天实例](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

部分：

- [设置项目](#setup)
- [运行示例](#run)
- [检查代码](#code)
- [后续步骤](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>设置项目

先决条件：

- Visual Studio 2010 SP1 中，Visual Studio 2012 或 Visual Studio 2012 Express。 如果未安装 Visual Studio，请参阅[ASP.NET 下载](https://www.asp.net/downloads)若要获取免费 Visual Studio 2012 Express 的开发工具。
- 对于 Visual Studio 2010，安装[ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683)。

本部分演示如何创建 ASP.NET MVC 4 应用程序、 添加 SignalR 库，并创建聊天应用程序。

1. 1. 在 Visual Studio 创建 ASP.NET MVC 4 应用程序，其命名为 SignalRChat，并单击确定。

        > [!NOTE]
        > 在 VS 2010 中，选择 **.NET Framework 4** Framework 版本下拉列表控件中。 SignalR 代码在.NET Framework 版本 4 和 4.5 上运行。

        ![创建 mvc web](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. 选择 Internet 应用程序模板，请清除选项**创建单元测试项目**，单击确定。

         ![创建 mvc internet 站点](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. 打开**工具 > NuGet 包管理器 > 程序包管理器控制台**并运行以下命令。 此步骤将一组脚本文件和启用 SignalR 功能的程序集引用添加到项目。

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. 在中**解决方案资源管理器**展开脚本文件夹。 请注意有关 SignalR 的脚本库已添加到项目。

         ![库引用](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. 在中**解决方案资源管理器**，右键单击项目，选择**添加 |新的文件夹**，并添加名为的新文件夹**中心**。
      6. 右键单击**中心**文件夹中，单击**添加 |类**，并创建一个新 C# 类名为**ChatHub.cs**。 将此类用作 SignalR 服务器集线器将消息发送到所有客户端。

> [!NOTE]
> 如果你使用 Visual Studio 2012 并已安装[ASP.NET 和 Web Tools 2012.2 更新](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation)，可以使用新的 SignalR 项模板创建 hub 类。 为此，请右键单击**中心**文件夹中，单击**添加 |新项**，选择**SignalR Hub 类 (v1)**，并将命名类**ChatHub.cs**。


1. 中的代码替换**ChatHub**用下面的代码的类。

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. 打开**Global.asax**文件的项目，并添加对方法的调用`RouteTable.Routes.MapHubs();`中的代码的第一行作为`Application_Start`方法。 此代码注册 SignalR 集线器的默认路由，必须在调用之前注册的任何其他路由。 已完成`Application_Start`方法看起来如下例所示。

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. 编辑`HomeController`类中找到**controllers/Homecontroller.cs**并将以下方法添加到类。 此方法返回**聊天**将在稍后的步骤中创建的视图。

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. 在中单击右键`Chat`方法只需创建，然后单击**添加视图**创建新的视图文件。
5. 在**添加视图**对话框中，请确保为选中此复选框**使用布局或母版页页**（清除其他复选框），然后单击**添加**。

    ![添加视图](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. 编辑名为的新视图文件**Chat.cshtml**。 之后&lt;h2&gt;标记中，粘贴以下&lt;div&gt;部分和`@section scripts`到页的代码块。 此脚本可将页后，可以发送聊天消息，并显示来自服务器的消息。 下面的代码块中出现聊天视图的完整代码。

    > [!IMPORTANT]
    > 在您将 SignalR 和其他脚本库添加到你的 Visual Studio 项目中，包管理器可能安装版本比本主题中所示的版本较新的脚本。 请确保你的代码中的脚本引用与项目中安装的脚本库的版本相匹配。

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. **保存所有**项目。

<a id="run"></a>

## <a name="run-the-sample"></a>运行示例

1. 按 F5 以在调试模式下运行该项目。
2. 在浏览器地址行中，追加 **/home/聊天**到项目的默认页的 URL。 聊天页面加载中的浏览器实例并提示输入用户名称。

    ![输入用户名](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. 输入用户名称。
4. 从地址行中的浏览器复制 URL 并将其用于打开两个更多的浏览器实例。 在每个浏览器实例中，输入唯一的用户名称。
5. 在每个浏览器实例中，添加注释并单击**发送**。 注释应显示在浏览器的所有实例。

    > [!NOTE]
    > 此简单的聊天应用程序不维护服务器上的讨论上下文。 在中心广播到所有当前用户的注释。 加入聊天更高版本的用户将看到消息从时添加它们加入。
6. 以下屏幕截图显示在浏览器中运行的聊天应用程序。

    ![聊天浏览器](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. 在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。 如果使用 Internet Explorer 作为你的浏览器，此节点会显示在调试模式下。 没有名为的脚本文件**中心**在运行时动态生成 SignalR 库。 此文件管理的 jQuery 脚本和服务器端代码之间的通信。 如果使用 Internet Explorer 以外的浏览器，您还可以访问动态**中心**通过浏览到它直接，例如文件 http://mywebsite/signalr/hubs。

    ![生成的中心脚本](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a>检查代码

SignalR 聊天应用程序演示了两个基本的 SignalR 开发任务： 在服务器上的主要协调对象为创建中心和使用 SignalR jQuery 库来发送和接收消息。

### <a name="signalr-hubs"></a>SignalR 集线器

中的代码示例**ChatHub**类派生自**Microsoft.AspNet.SignalR.Hub**类。 派生自**中心**类是一种有用的方式来构建 SignalR 应用程序。 可以在中心类上创建的公共方法，然后通过调用从网页中的 jQuery 脚本来访问这些方法。

在聊天代码中，客户端调用**ChatHub.Send**方法发送一封新邮件。 在中心反过来将消息发送给所有客户端，通过调用**Clients.All.addNewMessageToPage**。

**发送**方法演示了多个中心概念：

- 集线器上声明的公共方法，以便客户端可以调用它们。
- 使用**Microsoft.AspNet.SignalR.Hub.Clients**属性来访问所有客户端连接到此中心。
- 在客户端上调用 jQuery 函数 (如`addNewMessageToPage`函数) 来更新客户端。

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR 和 jQuery

**Chat.cshtml**视图文件中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。 在代码中的基本任务创建对自动生成的代理为中心，声明函数的服务器可以调用内容推送到客户端，并启动连接来将消息发送到中心的引用。

下面的代码声明集线器代理。

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> 在 jQuery 中于服务器类及其成员的引用位于混合大小写。 代码示例将引用 C# **ChatHub**类中作为 jQuery **chatHub**。 如果你想要引用`ChatHub`类在 jQuery 中使用传统的 Pascal 大小写，就像在 C# 中，编辑 ChatHub.cs 类文件。 添加`using`语句来引用`Microsoft.AspNet.SignalR.Hubs`命名空间。 然后添加`HubName`归于`ChatHub`类，例如`[HubName("ChatHub")]`。 最后，更新到你的 jQuery 引用`ChatHub`类。


下面的代码演示如何在脚本中创建一个回调函数。 在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。 可选调用`htmlEncode`函数将显示一种方式，以 html 格式显示在页中，作为一种方法，以防止脚本注入之前编码消息内容。

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

下面的代码演示如何在中心打开的连接。 代码启动连接，然后将其传递函数来处理单击事件上**发送**聊天页中的按钮。

> [!NOTE]
> 此方法可确保事件处理程序在执行之前建立的连接。


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>后续步骤

您学习了 SignalR 是一个框架，用于构建实时 web 应用程序。 您还学习了几个 SignalR 开发任务： 如何将 SignalR 添加到 ASP.NET 应用程序、 如何创建 hub 类以及如何发送和接收来自中心的消息。

若要了解更高级的 SignalR 开发概念，请访问以下站点 SignalR 源代码和资源：

- [SignalR 项目](http://signalr.net)
- [SignalR Github 和示例](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
