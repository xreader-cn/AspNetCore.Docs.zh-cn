---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 教程：开始使用 SignalR 1.x |Microsoft Docs
author: bradygaster
description: 使用 ASP.NET SignalR 生成 HTML 页中的实时聊天应用程序。
ms.author: bradyg
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: b4b632a84e40aa0b73dfc7a30da0cf28249cc5b4
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836621"
---
<a name="tutorial-getting-started-with-signalr-1x"></a>教程：开始使用 SignalR 1.x
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tim Teebken](https://github.com/timlt)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本教程介绍如何使用 SignalR 创建实时聊天应用程序。 将 SignalR 添加到空的 ASP.NET web 应用程序，并创建 HTML 页以发送和显示的消息。


## <a name="overview"></a>概述

本教程介绍了通过演示如何生成简单的基于浏览器的聊天应用程序的 SignalR 开发。 将 SignalR 库添加到空的 ASP.NET web 应用程序，创建一个中心类，用于将消息发送到客户端，并创建使用户能够发送和接收聊天消息的 HTML 页。 有关演示如何在 MVC 4 中创建的聊天应用程序使用 MVC 视图的类似教程，请参阅[SignalR 和 MVC 4 入门](index.md)。

> [!NOTE]
> 本教程使用 SignalR 的发布 (1.x) 版本。 有关 SignalR 之间的更改的详细信息 1.x 和 2.0 中，请参阅[升级 SignalR 1.x 项目](../releases/upgrading-signalr-1x-projects-to-20.md)。

SignalR 是构建 web 应用程序的需要实时用户交互或实时数据更新的开放源.NET 库。 示例包括社交应用程序、 多用户游戏、 业务协作和新闻、 天气或财务更新应用程序。 这些测试通常称为实时应用程序。

SignalR 简化了构建实时应用程序的过程。 它包括 ASP.NET 服务器库和 JavaScript 客户端库来轻松地管理客户端-服务器连接，并将内容更新推送到客户端。 您可以将 SignalR 库添加到现有 ASP.NET 应用程序以获取实时功能。

本教程演示以下的 SignalR 开发任务：

- SignalR 库添加到 ASP.NET web 应用程序。
- 创建用于将内容推送到客户端的中心类。
- 使用 SignalR jQuery 库在网页上发送消息并显示从中心的更新。

以下屏幕截图显示在浏览器中运行的聊天应用程序。 每个新用户可以发表评论，并查看用户加入聊天后已添加注释。

![聊天实例](tutorial-getting-started-with-signalr/_static/image1.png)

部分：

- [设置项目](#setup)
- [运行示例](#run)
- [检查代码](#code)
- [后续步骤](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>设置项目

本部分演示如何创建空的 ASP.NET web 应用程序，将 SignalR 中，并创建聊天应用程序。

先决条件：

- Visual Studio 2010 SP1 或 2012年。 如果未安装 Visual Studio，请参阅[ASP.NET 下载](https://www.asp.net/downloads)若要获取免费 Visual Studio 2012 Express 的开发工具。
- [Microsoft ASP.NET 和 Web 工具 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941)。 对于 Visual Studio 2012 中，此安装程序将添加新的 ASP.NET 功能包括对 Visual Studio 的 SignalR 模板。 对于 Visual Studio 2010 SP1，安装程序不可用，但可以通过安装 SignalR NuGet 包的安装步骤中所述完成该教程。

以下步骤使用 Visual Studio 2012 创建 ASP.NET 空 Web 应用程序并添加 SignalR 库：

1. 在 Visual Studio 创建 ASP.NET 空 Web 应用程序。

    ![创建空的 web](tutorial-getting-started-with-signalr/_static/image2.png)
2. 打开**程序包管理器控制台**通过选择**工具 |NuGet 包管理器 |包管理器控制台**。 控制台窗口中输入以下命令：

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    此命令将安装最新版本的 SignalR 1.x。
3. 在中**解决方案资源管理器**，右键单击项目，选择**添加 |类**。 将新类命名**ChatHub**。
4. 在中**解决方案资源管理器**展开脚本节点。 适用于 jQuery 和 SignalR 的脚本库将显示在该项目。

    ![库引用](tutorial-getting-started-with-signalr/_static/image3.png)
5. 中的代码替换**ChatHub**用下面的代码的类。

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. 在中**解决方案资源管理器**，右键单击项目，然后单击**添加 |新项**。 在中**添加新项**对话框中，选择**全局应用程序类**然后单击**添加**。

    ![添加全局](tutorial-getting-started-with-signalr/_static/image4.png)
7. 添加以下`using`语句之后提供`using`Global.asax.cs 类中的语句。

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. 添加以下行中的代码`Application_Start`要注册 SignalR 集线器的默认路由的全局类的方法。

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. 在中**解决方案资源管理器**，右键单击项目，然后单击**添加 |新项**。 在中**添加新项**对话框中，选择 Html 页面，然后单击**添加**。
10. 在中**解决方案资源管理器**，右键单击刚创建的 HTML 页，然后单击**设为起始页**。
11. HTML 页中的默认代码替换为以下代码。

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. **保存所有**项目。

<a id="run"></a>

## <a name="run-the-sample"></a>运行示例

1. 按 F5 以在调试模式下运行该项目。 中的浏览器实例并提示输入用户名称的 HTML 页面加载。

    ![输入用户名](tutorial-getting-started-with-signalr/_static/image5.png)
2. 输入用户名称。
3. 从地址行中的浏览器复制 URL 并将其用于打开两个更多的浏览器实例。 在每个浏览器实例中，输入唯一的用户名称。
4. 在每个浏览器实例中，添加注释并单击**发送**。 注释应显示在浏览器的所有实例。

    > [!NOTE]
    > 此简单的聊天应用程序不维护服务器上的讨论上下文。 在中心广播到所有当前用户的注释。 加入聊天更高版本的用户将看到消息从时添加它们加入。

    下面的屏幕截图显示了在三个浏览器情况下，所有这些更新一个实例发送消息时运行的聊天应用程序：

    ![聊天浏览器](tutorial-getting-started-with-signalr/_static/image6.png)
5. 在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。 没有名为的脚本文件**中心**在运行时动态生成 SignalR 库。 此文件管理的 jQuery 脚本和服务器端代码之间的通信。

    ![生成的中心脚本](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a>检查代码

SignalR 聊天应用程序演示了两个基本的 SignalR 开发任务： 在服务器上的主要协调对象为创建中心和使用 SignalR jQuery 库来发送和接收消息。

### <a name="signalr-hubs"></a>SignalR 集线器

中的代码示例**ChatHub**类派生自**Microsoft.AspNet.SignalR.Hub**类。 派生自**中心**类是一种有用的方式来构建 SignalR 应用程序。 可以在中心类上创建的公共方法，然后通过调用从网页中的 jQuery 脚本来访问这些方法。

在聊天代码中，客户端调用**ChatHub.Send**方法发送一封新邮件。 在中心反过来将消息发送给所有客户端，通过调用**Clients.All.broadcastMessage**。

**发送**方法演示了多个中心概念：

- 集线器上声明的公共方法，以便客户端可以调用它们。
- 使用**Microsoft.AspNet.SignalR.Hub.Clients**动态属性来访问所有客户端连接到此中心。
- 在客户端上调用 jQuery 函数 (如`broadcastMessage`函数) 来更新客户端。

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR 和 jQuery

HTML 页中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。 在代码中的基本任务声明的代理服务器能够引用中心，声明服务器可以将内容推送到客户端，调用的函数和启动的连接将消息发送到中心。

下面的代码声明集线器代理。

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> 在 jQuery 中于服务器类及其成员的引用位于混合大小写。 代码示例将引用 C# **ChatHub**类中作为 jQuery **chatHub**。


下面的代码是如何在脚本中创建一个回调函数。 在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。 HTML 显示前先编码内容的两行都是可选的并显示简单的方法来阻止脚本注入。

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

下面的代码演示如何在中心打开的连接。 代码启动连接，然后将其传递函数来处理单击事件上**发送**HTML 页中的按钮。

> [!NOTE]
> 此方法确保了事件处理程序在执行之前建立的连接。


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>后续步骤

您学习了 SignalR 是一个框架，用于构建实时 web 应用程序。 您还学习了几个 SignalR 开发任务： 如何将 SignalR 添加到 ASP.NET 应用程序、 如何创建 hub 类以及如何发送和接收来自中心的消息。

您可以提供的示例应用程序在本教程中或其他 SignalR 应用程序通过 Internet 将它们部署到托管提供商。 Microsoft 提供了免费的 web 承载的最多 10 个 web 站点中的免费[Windows Azure 试用帐户](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604)。 有关如何部署示例 SignalR 应用程序的演练，请参阅[发布 SignalR 入门示例作为 Windows Azure 网站](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx)。 有关如何将 Visual Studio web 项目部署到 Windows Azure 网站的详细信息，请参阅[部署 ASP.NET 应用程序到 Windows Azure 网站](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)。 （注意：WebSocket 传输当前不支持适用于 Windows Azure 网站。 当 WebSocket 传输不可用，SignalR 使用的其他可用的传输中的传输部分所述[SignalR 主题简介](index.md)。)

若要了解更高级的 SignalR 开发概念，请访问以下站点 SignalR 源代码和资源：

- [SignalR 项目](http://signalr.net)
- [SignalR Github 和示例](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
