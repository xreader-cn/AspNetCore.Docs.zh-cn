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
<a name="tutorial-getting-started-with-signalr-1x"></a><span data-ttu-id="f4778-103">教程：开始使用 SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="f4778-103">Tutorial: Getting Started with SignalR 1.x</span></span>
====================
<span data-ttu-id="f4778-104">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="f4778-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="f4778-105">本教程介绍如何使用 SignalR 创建实时聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-105">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="f4778-106">将 SignalR 添加到空的 ASP.NET web 应用程序，并创建 HTML 页以发送和显示的消息。</span><span class="sxs-lookup"><span data-stu-id="f4778-106">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="f4778-107">概述</span><span class="sxs-lookup"><span data-stu-id="f4778-107">Overview</span></span>

<span data-ttu-id="f4778-108">本教程介绍了通过演示如何生成简单的基于浏览器的聊天应用程序的 SignalR 开发。</span><span class="sxs-lookup"><span data-stu-id="f4778-108">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="f4778-109">将 SignalR 库添加到空的 ASP.NET web 应用程序，创建一个中心类，用于将消息发送到客户端，并创建使用户能够发送和接收聊天消息的 HTML 页。</span><span class="sxs-lookup"><span data-stu-id="f4778-109">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="f4778-110">有关演示如何在 MVC 4 中创建的聊天应用程序使用 MVC 视图的类似教程，请参阅[SignalR 和 MVC 4 入门](index.md)。</span><span class="sxs-lookup"><span data-stu-id="f4778-110">For a similar tutorial that shows how to create a chat application in MVC 4 using an MVC view, see [Getting Started with SignalR and MVC 4](index.md).</span></span>

> [!NOTE]
> <span data-ttu-id="f4778-111">本教程使用 SignalR 的发布 (1.x) 版本。</span><span class="sxs-lookup"><span data-stu-id="f4778-111">This tutorial uses the release (1.x) version of SignalR.</span></span> <span data-ttu-id="f4778-112">有关 SignalR 之间的更改的详细信息 1.x 和 2.0 中，请参阅[升级 SignalR 1.x 项目](../releases/upgrading-signalr-1x-projects-to-20.md)。</span><span class="sxs-lookup"><span data-stu-id="f4778-112">For details on changes between SignalR 1.x and 2.0, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md).</span></span>

<span data-ttu-id="f4778-113">SignalR 是构建 web 应用程序的需要实时用户交互或实时数据更新的开放源.NET 库。</span><span class="sxs-lookup"><span data-stu-id="f4778-113">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="f4778-114">示例包括社交应用程序、 多用户游戏、 业务协作和新闻、 天气或财务更新应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-114">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="f4778-115">这些测试通常称为实时应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-115">These are often called real-time applications.</span></span>

<span data-ttu-id="f4778-116">SignalR 简化了构建实时应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="f4778-116">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="f4778-117">它包括 ASP.NET 服务器库和 JavaScript 客户端库来轻松地管理客户端-服务器连接，并将内容更新推送到客户端。</span><span class="sxs-lookup"><span data-stu-id="f4778-117">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="f4778-118">您可以将 SignalR 库添加到现有 ASP.NET 应用程序以获取实时功能。</span><span class="sxs-lookup"><span data-stu-id="f4778-118">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="f4778-119">本教程演示以下的 SignalR 开发任务：</span><span class="sxs-lookup"><span data-stu-id="f4778-119">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="f4778-120">SignalR 库添加到 ASP.NET web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-120">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="f4778-121">创建用于将内容推送到客户端的中心类。</span><span class="sxs-lookup"><span data-stu-id="f4778-121">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="f4778-122">使用 SignalR jQuery 库在网页上发送消息并显示从中心的更新。</span><span class="sxs-lookup"><span data-stu-id="f4778-122">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="f4778-123">以下屏幕截图显示在浏览器中运行的聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-123">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="f4778-124">每个新用户可以发表评论，并查看用户加入聊天后已添加注释。</span><span class="sxs-lookup"><span data-stu-id="f4778-124">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![聊天实例](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="f4778-126">部分：</span><span class="sxs-lookup"><span data-stu-id="f4778-126">Sections:</span></span>

- [<span data-ttu-id="f4778-127">设置项目</span><span class="sxs-lookup"><span data-stu-id="f4778-127">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="f4778-128">运行示例</span><span class="sxs-lookup"><span data-stu-id="f4778-128">Run the Sample</span></span>](#run)
- [<span data-ttu-id="f4778-129">检查代码</span><span class="sxs-lookup"><span data-stu-id="f4778-129">Examine the Code</span></span>](#code)
- [<span data-ttu-id="f4778-130">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f4778-130">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="f4778-131">设置项目</span><span class="sxs-lookup"><span data-stu-id="f4778-131">Set up the Project</span></span>

<span data-ttu-id="f4778-132">本部分演示如何创建空的 ASP.NET web 应用程序，将 SignalR 中，并创建聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-132">This section shows how to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="f4778-133">先决条件：</span><span class="sxs-lookup"><span data-stu-id="f4778-133">Prerequisites:</span></span>

- <span data-ttu-id="f4778-134">Visual Studio 2010 SP1 或 2012年。</span><span class="sxs-lookup"><span data-stu-id="f4778-134">Visual Studio 2010 SP1 or 2012.</span></span> <span data-ttu-id="f4778-135">如果未安装 Visual Studio，请参阅[ASP.NET 下载](https://www.asp.net/downloads)若要获取免费 Visual Studio 2012 Express 的开发工具。</span><span class="sxs-lookup"><span data-stu-id="f4778-135">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="f4778-136">[Microsoft ASP.NET 和 Web 工具 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941)。</span><span class="sxs-lookup"><span data-stu-id="f4778-136">[Microsoft ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span></span> <span data-ttu-id="f4778-137">对于 Visual Studio 2012 中，此安装程序将添加新的 ASP.NET 功能包括对 Visual Studio 的 SignalR 模板。</span><span class="sxs-lookup"><span data-stu-id="f4778-137">For Visual Studio 2012, this installer adds new ASP.NET features including SignalR templates to Visual Studio.</span></span> <span data-ttu-id="f4778-138">对于 Visual Studio 2010 SP1，安装程序不可用，但可以通过安装 SignalR NuGet 包的安装步骤中所述完成该教程。</span><span class="sxs-lookup"><span data-stu-id="f4778-138">For Visual Studio 2010 SP1, an installer is not available but you can complete the tutorial by installing the SignalR NuGet package as described in the setup steps.</span></span>

<span data-ttu-id="f4778-139">以下步骤使用 Visual Studio 2012 创建 ASP.NET 空 Web 应用程序并添加 SignalR 库：</span><span class="sxs-lookup"><span data-stu-id="f4778-139">The following steps use Visual Studio 2012 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="f4778-140">在 Visual Studio 创建 ASP.NET 空 Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-140">In Visual Studio create an ASP.NET Empty Web Application.</span></span>

    ![创建空的 web](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="f4778-142">打开**程序包管理器控制台**通过选择**工具 |NuGet 包管理器 |包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="f4778-142">Open the **Package Manager Console** by selecting **Tools | NuGet Package Manager | Package Manager Console**.</span></span> <span data-ttu-id="f4778-143">控制台窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="f4778-143">Enter the following command into the console window:</span></span>

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    <span data-ttu-id="f4778-144">此命令将安装最新版本的 SignalR 1.x。</span><span class="sxs-lookup"><span data-stu-id="f4778-144">This command installs the latest version of SignalR 1.x.</span></span>
3. <span data-ttu-id="f4778-145">在中**解决方案资源管理器**，右键单击项目，选择**添加 |类**。</span><span class="sxs-lookup"><span data-stu-id="f4778-145">In **Solution Explorer**, right-click the project, select **Add | Class**.</span></span> <span data-ttu-id="f4778-146">将新类命名**ChatHub**。</span><span class="sxs-lookup"><span data-stu-id="f4778-146">Name the new class **ChatHub**.</span></span>
4. <span data-ttu-id="f4778-147">在中**解决方案资源管理器**展开脚本节点。</span><span class="sxs-lookup"><span data-stu-id="f4778-147">In **Solution Explorer** expand the Scripts node.</span></span> <span data-ttu-id="f4778-148">适用于 jQuery 和 SignalR 的脚本库将显示在该项目。</span><span class="sxs-lookup"><span data-stu-id="f4778-148">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    ![库引用](tutorial-getting-started-with-signalr/_static/image3.png)
5. <span data-ttu-id="f4778-150">中的代码替换**ChatHub**用下面的代码的类。</span><span class="sxs-lookup"><span data-stu-id="f4778-150">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="f4778-151">在中**解决方案资源管理器**，右键单击项目，然后单击**添加 |新项**。</span><span class="sxs-lookup"><span data-stu-id="f4778-151">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="f4778-152">在中**添加新项**对话框中，选择**全局应用程序类**然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="f4778-152">In the **Add New Item** dialog, select **Global Application Class** and click **Add**.</span></span>

    ![添加全局](tutorial-getting-started-with-signalr/_static/image4.png)
7. <span data-ttu-id="f4778-154">添加以下`using`语句之后提供`using`Global.asax.cs 类中的语句。</span><span class="sxs-lookup"><span data-stu-id="f4778-154">Add the following `using` statements after the provided `using` statements in the Global.asax.cs class.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="f4778-155">添加以下行中的代码`Application_Start`要注册 SignalR 集线器的默认路由的全局类的方法。</span><span class="sxs-lookup"><span data-stu-id="f4778-155">Add the following line of code in the `Application_Start` method of the Global class to register the default route for SignalR hubs.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. <span data-ttu-id="f4778-156">在中**解决方案资源管理器**，右键单击项目，然后单击**添加 |新项**。</span><span class="sxs-lookup"><span data-stu-id="f4778-156">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="f4778-157">在中**添加新项**对话框中，选择 Html 页面，然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="f4778-157">In the **Add New Item** dialog, select Html Page and click **Add**.</span></span>
10. <span data-ttu-id="f4778-158">在中**解决方案资源管理器**，右键单击刚创建的 HTML 页，然后单击**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="f4778-158">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
11. <span data-ttu-id="f4778-159">HTML 页中的默认代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="f4778-159">Replace the default code in the HTML page with the following code.</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. <span data-ttu-id="f4778-160">**保存所有**项目。</span><span class="sxs-lookup"><span data-stu-id="f4778-160">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="f4778-161">运行示例</span><span class="sxs-lookup"><span data-stu-id="f4778-161">Run the Sample</span></span>

1. <span data-ttu-id="f4778-162">按 F5 以在调试模式下运行该项目。</span><span class="sxs-lookup"><span data-stu-id="f4778-162">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="f4778-163">中的浏览器实例并提示输入用户名称的 HTML 页面加载。</span><span class="sxs-lookup"><span data-stu-id="f4778-163">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![输入用户名](tutorial-getting-started-with-signalr/_static/image5.png)
2. <span data-ttu-id="f4778-165">输入用户名称。</span><span class="sxs-lookup"><span data-stu-id="f4778-165">Enter a user name.</span></span>
3. <span data-ttu-id="f4778-166">从地址行中的浏览器复制 URL 并将其用于打开两个更多的浏览器实例。</span><span class="sxs-lookup"><span data-stu-id="f4778-166">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="f4778-167">在每个浏览器实例中，输入唯一的用户名称。</span><span class="sxs-lookup"><span data-stu-id="f4778-167">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="f4778-168">在每个浏览器实例中，添加注释并单击**发送**。</span><span class="sxs-lookup"><span data-stu-id="f4778-168">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="f4778-169">注释应显示在浏览器的所有实例。</span><span class="sxs-lookup"><span data-stu-id="f4778-169">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="f4778-170">此简单的聊天应用程序不维护服务器上的讨论上下文。</span><span class="sxs-lookup"><span data-stu-id="f4778-170">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="f4778-171">在中心广播到所有当前用户的注释。</span><span class="sxs-lookup"><span data-stu-id="f4778-171">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="f4778-172">加入聊天更高版本的用户将看到消息从时添加它们加入。</span><span class="sxs-lookup"><span data-stu-id="f4778-172">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="f4778-173">下面的屏幕截图显示了在三个浏览器情况下，所有这些更新一个实例发送消息时运行的聊天应用程序：</span><span class="sxs-lookup"><span data-stu-id="f4778-173">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![聊天浏览器](tutorial-getting-started-with-signalr/_static/image6.png)
5. <span data-ttu-id="f4778-175">在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-175">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="f4778-176">没有名为的脚本文件**中心**在运行时动态生成 SignalR 库。</span><span class="sxs-lookup"><span data-stu-id="f4778-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="f4778-177">此文件管理的 jQuery 脚本和服务器端代码之间的通信。</span><span class="sxs-lookup"><span data-stu-id="f4778-177">This file manages the communication between jQuery script and server-side code.</span></span>

    ![生成的中心脚本](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="f4778-179">检查代码</span><span class="sxs-lookup"><span data-stu-id="f4778-179">Examine the Code</span></span>

<span data-ttu-id="f4778-180">SignalR 聊天应用程序演示了两个基本的 SignalR 开发任务： 在服务器上的主要协调对象为创建中心和使用 SignalR jQuery 库来发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="f4778-180">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="f4778-181">SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="f4778-181">SignalR Hubs</span></span>

<span data-ttu-id="f4778-182">中的代码示例**ChatHub**类派生自**Microsoft.AspNet.SignalR.Hub**类。</span><span class="sxs-lookup"><span data-stu-id="f4778-182">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="f4778-183">派生自**中心**类是一种有用的方式来构建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-183">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="f4778-184">可以在中心类上创建的公共方法，然后通过调用从网页中的 jQuery 脚本来访问这些方法。</span><span class="sxs-lookup"><span data-stu-id="f4778-184">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="f4778-185">在聊天代码中，客户端调用**ChatHub.Send**方法发送一封新邮件。</span><span class="sxs-lookup"><span data-stu-id="f4778-185">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="f4778-186">在中心反过来将消息发送给所有客户端，通过调用**Clients.All.broadcastMessage**。</span><span class="sxs-lookup"><span data-stu-id="f4778-186">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="f4778-187">**发送**方法演示了多个中心概念：</span><span class="sxs-lookup"><span data-stu-id="f4778-187">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="f4778-188">集线器上声明的公共方法，以便客户端可以调用它们。</span><span class="sxs-lookup"><span data-stu-id="f4778-188">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="f4778-189">使用**Microsoft.AspNet.SignalR.Hub.Clients**动态属性来访问所有客户端连接到此中心。</span><span class="sxs-lookup"><span data-stu-id="f4778-189">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="f4778-190">在客户端上调用 jQuery 函数 (如`broadcastMessage`函数) 来更新客户端。</span><span class="sxs-lookup"><span data-stu-id="f4778-190">Call a jQuery function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="f4778-191">SignalR 和 jQuery</span><span class="sxs-lookup"><span data-stu-id="f4778-191">SignalR and jQuery</span></span>

<span data-ttu-id="f4778-192">HTML 页中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。</span><span class="sxs-lookup"><span data-stu-id="f4778-192">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="f4778-193">在代码中的基本任务声明的代理服务器能够引用中心，声明服务器可以将内容推送到客户端，调用的函数和启动的连接将消息发送到中心。</span><span class="sxs-lookup"><span data-stu-id="f4778-193">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="f4778-194">下面的代码声明集线器代理。</span><span class="sxs-lookup"><span data-stu-id="f4778-194">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="f4778-195">在 jQuery 中于服务器类及其成员的引用位于混合大小写。</span><span class="sxs-lookup"><span data-stu-id="f4778-195">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="f4778-196">代码示例将引用 C# **ChatHub**类中作为 jQuery **chatHub**。</span><span class="sxs-lookup"><span data-stu-id="f4778-196">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span>


<span data-ttu-id="f4778-197">下面的代码是如何在脚本中创建一个回调函数。</span><span class="sxs-lookup"><span data-stu-id="f4778-197">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="f4778-198">在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。</span><span class="sxs-lookup"><span data-stu-id="f4778-198">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="f4778-199">HTML 显示前先编码内容的两行都是可选的并显示简单的方法来阻止脚本注入。</span><span class="sxs-lookup"><span data-stu-id="f4778-199">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

<span data-ttu-id="f4778-200">下面的代码演示如何在中心打开的连接。</span><span class="sxs-lookup"><span data-stu-id="f4778-200">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="f4778-201">代码启动连接，然后将其传递函数来处理单击事件上**发送**HTML 页中的按钮。</span><span class="sxs-lookup"><span data-stu-id="f4778-201">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="f4778-202">此方法确保了事件处理程序在执行之前建立的连接。</span><span class="sxs-lookup"><span data-stu-id="f4778-202">This approach insures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="f4778-203">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f4778-203">Next Steps</span></span>

<span data-ttu-id="f4778-204">您学习了 SignalR 是一个框架，用于构建实时 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f4778-204">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="f4778-205">您还学习了几个 SignalR 开发任务： 如何将 SignalR 添加到 ASP.NET 应用程序、 如何创建 hub 类以及如何发送和接收来自中心的消息。</span><span class="sxs-lookup"><span data-stu-id="f4778-205">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="f4778-206">您可以提供的示例应用程序在本教程中或其他 SignalR 应用程序通过 Internet 将它们部署到托管提供商。</span><span class="sxs-lookup"><span data-stu-id="f4778-206">You can make the sample application in this tutorial or other SignalR applications available over the Internet by deploying them to a hosting provider.</span></span> <span data-ttu-id="f4778-207">Microsoft 提供了免费的 web 承载的最多 10 个 web 站点中的免费[Windows Azure 试用帐户](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604)。</span><span class="sxs-lookup"><span data-stu-id="f4778-207">Microsoft offers free web hosting for up to 10 web sites in a free [Windows Azure trial account](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="f4778-208">有关如何部署示例 SignalR 应用程序的演练，请参阅[发布 SignalR 入门示例作为 Windows Azure 网站](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f4778-208">For a walkthrough on how to deploy the sample SignalR application, see [Publish the SignalR Getting Started Sample as a Windows Azure Web Site](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span></span> <span data-ttu-id="f4778-209">有关如何将 Visual Studio web 项目部署到 Windows Azure 网站的详细信息，请参阅[部署 ASP.NET 应用程序到 Windows Azure 网站](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="f4778-209">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Deploying an ASP.NET Application to a Windows Azure Web Site](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span></span> <span data-ttu-id="f4778-210">（注意：WebSocket 传输当前不支持适用于 Windows Azure 网站。</span><span class="sxs-lookup"><span data-stu-id="f4778-210">(Note: The WebSocket transport is not currently supported for Windows Azure Web Sites.</span></span> <span data-ttu-id="f4778-211">当 WebSocket 传输不可用，SignalR 使用的其他可用的传输中的传输部分所述[SignalR 主题简介](index.md)。)</span><span class="sxs-lookup"><span data-stu-id="f4778-211">When WebSocket transport is not available, SignalR uses the other available transports as described in the Transports section of the [Introduction to SignalR topic](index.md).)</span></span>

<span data-ttu-id="f4778-212">若要了解更高级的 SignalR 开发概念，请访问以下站点 SignalR 源代码和资源：</span><span class="sxs-lookup"><span data-stu-id="f4778-212">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="f4778-213">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="f4778-213">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="f4778-214">SignalR Github 和示例</span><span class="sxs-lookup"><span data-stu-id="f4778-214">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="f4778-215">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="f4778-215">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
