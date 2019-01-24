---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: 将 SignalR 1.x 项目升级到版本 2 |Microsoft Docs
author: bradygaster
description: 本主题介绍如何升级现有的 SignalR 1.x 项目到 SignalR 2.x 中，以及如何解决在升级过程中可能出现的问题...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: a1cb4903f3cdeef70ffd0f624a3a2170f641a395
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837333"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a>将 SignalR 1.x 项目升级到版本 2
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本主题介绍如何升级现有的 SignalR 1.x 项目到 SignalR 2.x 中，以及如何解决在升级过程中可能出现的问题。
>
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR 版本 1 和 2
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>本教程使用 Visual Studio 2012
>
>
> 若要学习本教程使用 Visual Studio 2012，请执行以下操作：
>
> - 更新你[程序包管理器](http://docs.nuget.org/docs/start-here/installing-nuget)到最新版本。
> - 安装[Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。
> - 在 Web 平台安装程序中，搜索并安装**ASP.NET 和 Web 工具 2013.1 适用于 Visual Studio 2012**。 这将安装 Visual Studio 模板的 SignalR 类，如**中心**。
> - 某些模板 (如**OWIN 启动类**) 将不可用; 对于这些数据，改为使用的类文件。
>
>
> ## <a name="questions-and-comments"></a>问题和提出的意见
>
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。


SignalR 2，在使用的服务器平台提供一致的开发体验[OWIN](http://owin.org)。 本文介绍的几个步骤所需更新到版本 2 的 SignalR 1.x 应用程序。

虽然建议升级应用程序到 SignalR 2，SignalR 1.x 仍受支持。

本教程介绍如何升级到 SignalR 2 web 承载的应用程序。 SignalR 2 下现在支持自承载应用程序 （这些托管在一个控制台应用程序、 Windows 服务或其他进程服务器）。 有关如何开始使用 SignalR 2 创建自承载的应用程序的信息，请参阅[教程：自承载 SignalR](../deployment/tutorial-signalr-self-host.md)。

## <a name="contents"></a>内容

以下部分介绍与升级 SignalR 项目，以及如何解决可能出现的问题涉及的任务。

- [示例：升级到 SignalR 2 入门教程](#example)
- [在升级过程中遇到的错误疑难解答](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a>示例:入门教程应用程序升级到 SignalR 2

在本部分中，你将更新中创建的应用程序[SignalR 1.x 版的入门教程](../older-versions/index.md)使用 SignalR 2。

1. 完成入门教程后，右键单击项目，然后选择**属性**。 确认**目标框架**设置为 **.NET Framework 4.5。**
2. 打开包管理器控制台。 删除 SignalR 1.x 从项目使用以下命令：

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. 安装 SignalR 2： 使用以下命令：

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. 在 HTML 页中，更新 SignalR 以匹配的版本现在包含在项目中的脚本的脚本引用。

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. 在全局应用程序类中，删除对 MapHubs 的调用。

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. 右键单击解决方案，然后选择**外**，**新项...**.在对话框中，选择**Owin 启动类**。 将新类命名**Startup.cs**。

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. Startup.cs 的内容替换为以下代码：

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    程序集特性将类添加到 Owin 的启动过程中，后者执行`Configuration`Owin 启动时的方法。 此方法又调用`MapSignalR`方法，用于在应用程序中创建的所有 SignalR 集线器路由。
8. 运行该项目，并主页的 URL 复制到另一个浏览器或浏览器窗格中的，前面所述。 每个页面会要求提供用户名，并从每个页面发送的消息应在这两个浏览器窗格中可见。

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a>在升级过程中遇到的错误疑难解答

本部分介绍在升级过程中可能出现的问题。 有关更全面的 SignalR 应用程序可能发生的错误和问题列表，请参阅[SignalR 疑难解答](../testing-and-debugging/troubleshooting.md)。

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a>调用之间不明确的以下方法或属性

如果对的引用，将发生此错误`Microsoft.AspNet.SignalR.Owin`不会删除。 此包已弃用;必须删除的引用，必须卸载 SelfHost 包的 1.x 版本。

### <a name="hub-methods-fail-silently"></a>中心方法将以静默方式失败

验证你的客户端中的脚本引用是否最新，并的`OwinStartup`属性 (attribute) Startup 类具有正确的类和项目的程序集名称。 此外，请试着打开浏览器; 中的中心地址 （/signalr/中心）出现任何错误将提供有关什么问题的详细信息。
