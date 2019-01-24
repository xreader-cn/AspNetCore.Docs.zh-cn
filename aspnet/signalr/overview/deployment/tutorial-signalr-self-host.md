---
uid: signalr/overview/deployment/tutorial-signalr-self-host
title: 教程：自承载 SignalR |Microsoft Docs
author: bradygaster
description: 本教程演示如何创建自承载的 SignalR 2 服务器，以及如何使用 JavaScript 客户端连接到它。 在本教程 V 中使用的软件版本...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 400db427-27af-4f2f-abf0-5486d5e024b5
msc.legacyurl: /signalr/overview/deployment/tutorial-signalr-self-host
msc.type: authoredcontent
ms.openlocfilehash: 6a6359d59a4b715e13fe2bbcef57da6d6d6294b5
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835747"
---
<a name="tutorial-signalr-self-host"></a>教程：自承载 SignalR
====================
通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[下载已完成的项目](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> 本教程演示如何创建自承载的 SignalR 2 服务器，以及如何使用 JavaScript 客户端连接到它。
>
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR 版本 2
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


## <a name="overview"></a>概述

SignalR 服务器通常托管在 ASP.NET 应用程序在 IIS 中，但它也可以是自承载 （如控制台应用程序或 Windows 服务） 使用的自承载的库。 此库，如所有 SignalR 2 基于 OWIN ([Open Web Interface for.NET](http://owin.org))。 OWIN 定义.NET web 服务器和 web 应用程序之间的抽象。 OWIN 将从服务器中，这使 OWIN 适合于自承载在 IIS 外部自己进程中的 web 应用程序的 web 应用程序中分离出来。

不在 IIS 中承载的原因包括：

- IIS 不可用或有意义，如不含 IIS 的现有服务器场的位置的环境。
- 需要避免使用 IIS 的性能开销。
- SignalR 功能是添加到 Windows 服务、 Azure 辅助角色或其他进程中运行的现有应用程序。

如果正在开发一个解决方案作为自托管出于性能原因，建议测试也在 IIS 中托管的应用程序以确定的性能优势。

本教程包含以下各节：

- [创建服务器](#server)
- [访问与 JavaScript 客户端服务器](#js)

<a id="server"></a>

## <a name="creating-the-server"></a>创建服务器

在本教程中，你将在控制台应用程序中，创建托管的服务器，但服务器可以托管在任何类型的过程中，如 Windows 服务或 Azure 辅助角色。 有关示例代码用于托管 Windows 服务中的 SignalR 服务器，请参阅[Self-Hosting Windows 服务中的 SignalR](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3)。

1. 使用管理员特权打开 Visual Studio 2013。 选择**文件**，**新项目**。 选择**Windows**下**Visual C#** 中的节点**模板**窗格，然后选择**控制台应用程序**模板。 命名新项目"SignalRSelfHost"，然后单击**确定**。

    ![](tutorial-signalr-self-host/_static/image1.png)
2. 通过选择打开 NuGet 包管理器控制台**工具** > **NuGet 包管理器** > **程序包管理器控制台**。
3. 在包管理器控制台中，输入以下命令：

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    此命令将 SignalR 2 自托管库添加到项目。
4. 在包管理器控制台中，输入以下命令：

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    此命令将 Microsoft.Owin.Cors 库添加到项目。 跨域支持，这是必需的应用程序的托管 SignalR 和不同的域中的网页客户端将用于此库。 由于你将承载 SignalR 服务器和不同的端口上的 web 客户端，这意味着必须为这些组件之间的通信启用跨域。
5. 将 Program.cs 的内容替换为以下代码。

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    上面的代码包括三个类：

    - **程序**，其中包括**Main**定义执行主路径的方法。 在此方法中，类型的 web 应用程序**启动**开始时间为指定的 URL (`http://localhost:8080`)。 如果安全需要的终结点上，可以实现 SSL。 请参阅[如何：使用 SSL 证书配置端口](https://msdn.microsoft.com/library/ms733791.aspx)有关详细信息。
    - **启动**、 包含 SignalR 服务器的配置的类 (本教程使用的唯一配置是在调用`UseCors`)，以及对调用`MapSignalR`，用于在项目中创建路由的中心的任何对象。
    - **MyHub**，应用程序将向客户端提供的 SignalR Hub 类。 此类有一个单一的方法，**发送**，客户端将调用以将一条消息广播到所有其他连接的客户端。
6. 编译并运行该应用程序。 服务器正在运行的地址应显示在控制台窗口中。

    ![](tutorial-signalr-self-host/_static/image2.png)
7. 如果执行失败，出现异常`System.Reflection.TargetInvocationException was unhandled`，将需要使用管理员权限重新启动 Visual Studio。
8. 停止应用程序，然后再继续执行下一节。

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a>访问与 JavaScript 客户端服务器

在本部分中，您将使用从相同的 JavaScript 客户端[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)。 我们将仅向客户端，则需要显式定义中心 URL 的一处修改。 与自承载的应用程序，服务器不一定是在相同的地址作为连接的 URL （由于反向代理和负载均衡器），因此需要显式定义该 URL。

1. 在中**解决方案资源管理器**，右键单击解决方案并选择**添加**，**新项目**。 选择**Web**节点，然后选择**ASP.NET Web 应用程序**模板。 命名项目"JavascriptClient"，然后单击**确定**。

    ![](tutorial-signalr-self-host/_static/image3.png)
2. 选择**空**模板，然后保留剩余的选项未选中。 选择**创建项目**。

    ![](tutorial-signalr-self-host/_static/image4.png)
3. 在包管理器控制台中，选择"JavascriptClient"项目中的**默认项目**下拉列表中，并执行以下命令：

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    此命令将安装在客户端中将需要的 SignalR 和 JQuery 库。
4. 右键单击项目并选择**外**，**新项**。 选择**Web**节点，然后选择 HTML 页。 将该页命名为**Default.html**。

    ![](tutorial-signalr-self-host/_static/image5.png)
5. 将以下代码替换为新的 HTML 页面的内容。 验证在此处的脚本引用匹配项目的 Scripts 文件夹中的脚本。

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    下面的代码 （在上面的代码示例中突出显示） 是对 （除了为 SignalR 版本 2 beta 版本升级代码） 获取开始本教程中使用的客户端所做的添加。 这行代码显式设置的基本连接 URL SignalR 在服务器上。

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. 右键单击解决方案，然后选择**设置启动项目...**.选择**多个启动项目**单选按钮，并设置这两个项目的**操作**到**启动**。

    ![](tutorial-signalr-self-host/_static/image6.png)
7. 右键单击"Default.html"，然后选择**设为起始页**。
8. 运行该应用程序。 服务器和页上将启动。 可能需要重新加载该网页 (或选择**继续**在调试器中) 如果页面可加载之前启动服务器。
9. 在浏览器提供用出现提示时的用户名。 将页面的 URL 复制到另一个浏览器选项卡或窗口，并提供其他用户名。 你将能够将消息从一个浏览器窗格中发送到另一个，如入门教程中所示。
