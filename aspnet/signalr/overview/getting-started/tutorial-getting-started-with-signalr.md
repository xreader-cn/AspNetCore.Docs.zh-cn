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
# <a name="tutorial-real-time-chat-with-signalr-2"></a><span data-ttu-id="9fd30-104">教程：与 SignalR 2 实时聊天</span><span class="sxs-lookup"><span data-stu-id="9fd30-104">Tutorial: Real-time chat with SignalR 2</span></span>

<span data-ttu-id="9fd30-105">本教程演示如何使用 SignalR 创建实时聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="9fd30-105">This tutorial shows you how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="9fd30-106">将 SignalR 添加到空的 ASP.NET web 应用程序和创建 HTML 页以发送和显示的消息。</span><span class="sxs-lookup"><span data-stu-id="9fd30-106">You add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>

<span data-ttu-id="9fd30-107">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9fd30-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9fd30-108">设置项目</span><span class="sxs-lookup"><span data-stu-id="9fd30-108">Set up the project</span></span>
> * <span data-ttu-id="9fd30-109">运行示例</span><span class="sxs-lookup"><span data-stu-id="9fd30-109">Run the sample</span></span>
> * <span data-ttu-id="9fd30-110">检查代码</span><span class="sxs-lookup"><span data-stu-id="9fd30-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="9fd30-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="9fd30-111">Prerequisites</span></span>

* <span data-ttu-id="9fd30-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="9fd30-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="9fd30-113">设置项目</span><span class="sxs-lookup"><span data-stu-id="9fd30-113">Set up the Project</span></span>

<span data-ttu-id="9fd30-114">本部分演示如何使用 Visual Studio 2017 和 SignalR 2 来创建空的 ASP.NET web 应用程序，将 SignalR 中，并创建聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="9fd30-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

1. <span data-ttu-id="9fd30-115">在 Visual Studio 中创建 ASP.NET Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9fd30-115">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![创建 web](tutorial-getting-started-with-signalr/_static/image2.png)

1. <span data-ttu-id="9fd30-117">在中**新建 ASP.NET 项目-SignalRChat**窗口中，保留**空**，并选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-117">In the **New ASP.NET Project - SignalRChat** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="9fd30-118">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-118">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="9fd30-119">在中**添加新项-SignalRChat**，选择**已安装** > **Visual C#**   >  **Web**  > **SignalR** ，然后选择**SignalR Hub 类 (v2)**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-119">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="9fd30-120">将类命名*ChatHub*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="9fd30-120">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="9fd30-121">此步骤将创建*ChatHub.cs*类文件并添加一组脚本文件和 SignalR 支持到项目的程序集引用。</span><span class="sxs-lookup"><span data-stu-id="9fd30-121">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="9fd30-122">在新的代码替换为*ChatHub.cs*类文件，此代码：</span><span class="sxs-lookup"><span data-stu-id="9fd30-122">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="9fd30-123">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-123">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="9fd30-124">在中**添加新项-SignalRChat**选择**已安装** > **Visual C#**   >  **Web** ，然后选择**OWIN 启动类**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-124">In **Add New Item - SignalRChat** select **Installed** > **Visual C#** > **Web**  and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="9fd30-125">将类命名*启动*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="9fd30-125">Name the class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="9fd30-126">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **HTML 页**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-126">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="9fd30-127">命名新页面*索引*，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-127">Name the new page *index* and select **OK**.</span></span>

1. <span data-ttu-id="9fd30-128">在中**解决方案资源管理器**，右键单击你创建的 HTML 页面，然后选择**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-128">In **Solution Explorer**, right-click the HTML page you created and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="9fd30-129">HTML 页中的默认代码替换此代码：</span><span class="sxs-lookup"><span data-stu-id="9fd30-129">Replace the default code in the HTML page with this code:</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. <span data-ttu-id="9fd30-130">在中**解决方案资源管理器**，展开**脚本**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-130">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="9fd30-131">适用于 jQuery 和 SignalR 的脚本库将显示在该项目。</span><span class="sxs-lookup"><span data-stu-id="9fd30-131">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="9fd30-132">包管理器可能已安装的 SignalR 脚本的更高版本。</span><span class="sxs-lookup"><span data-stu-id="9fd30-132">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="9fd30-133">检查与项目中的脚本文件的版本相对应的代码块中的脚本引用。</span><span class="sxs-lookup"><span data-stu-id="9fd30-133">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="9fd30-134">从原始的代码块的脚本引用：</span><span class="sxs-lookup"><span data-stu-id="9fd30-134">Script references from the original code block:</span></span>
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. <span data-ttu-id="9fd30-135">如果不匹配，更新 *.html*文件。</span><span class="sxs-lookup"><span data-stu-id="9fd30-135">If they don't match, update the *.html* file.</span></span>

1. <span data-ttu-id="9fd30-136">从菜单栏中，选择**文件** > **全部保存**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-136">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="9fd30-137">运行示例</span><span class="sxs-lookup"><span data-stu-id="9fd30-137">Run the Sample</span></span>

1. <span data-ttu-id="9fd30-138">在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行示例。</span><span class="sxs-lookup"><span data-stu-id="9fd30-138">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![输入用户名](tutorial-getting-started-with-signalr/_static/image3.png)

1. <span data-ttu-id="9fd30-140">在浏览器打开时，输入你的聊天身份的名称。</span><span class="sxs-lookup"><span data-stu-id="9fd30-140">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="9fd30-141">从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="9fd30-141">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="9fd30-142">在每个浏览器中，输入唯一名称。</span><span class="sxs-lookup"><span data-stu-id="9fd30-142">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="9fd30-143">现在，添加注释并选择**发送**。</span><span class="sxs-lookup"><span data-stu-id="9fd30-143">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="9fd30-144">在其他浏览器中重复的。</span><span class="sxs-lookup"><span data-stu-id="9fd30-144">Repeat that in the other browsers.</span></span> <span data-ttu-id="9fd30-145">注释将显示在实时中。</span><span class="sxs-lookup"><span data-stu-id="9fd30-145">The comments appear in real-time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="9fd30-146">此简单的聊天应用程序不维护服务器上的讨论上下文。</span><span class="sxs-lookup"><span data-stu-id="9fd30-146">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="9fd30-147">在中心广播到所有当前用户的注释。</span><span class="sxs-lookup"><span data-stu-id="9fd30-147">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="9fd30-148">加入聊天更高版本的用户将看到消息从时添加它们加入。</span><span class="sxs-lookup"><span data-stu-id="9fd30-148">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="9fd30-149">请参阅聊天应用程序在三个不同的浏览器中的运行方式。</span><span class="sxs-lookup"><span data-stu-id="9fd30-149">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="9fd30-150">当 Tom、 Anand 和 Susan 发送消息时，所有浏览器实时更新：</span><span class="sxs-lookup"><span data-stu-id="9fd30-150">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![所有三个浏览器显示相同的聊天历史记录](tutorial-getting-started-with-signalr/_static/image4.png)

1. <span data-ttu-id="9fd30-152">在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="9fd30-152">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="9fd30-153">没有名为的脚本文件*中心*SignalR 库生成在运行时。</span><span class="sxs-lookup"><span data-stu-id="9fd30-153">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="9fd30-154">此文件管理的 jQuery 脚本和服务器端代码之间的通信。</span><span class="sxs-lookup"><span data-stu-id="9fd30-154">This file manages the communication between jQuery script and server-side code.</span></span>

    ![在脚本文档节点中自动生成中心脚本](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="9fd30-156">检查代码</span><span class="sxs-lookup"><span data-stu-id="9fd30-156">Examine the Code</span></span>

<span data-ttu-id="9fd30-157">SignalRChat 应用程序演示了两个基本的 SignalR 开发任务。</span><span class="sxs-lookup"><span data-stu-id="9fd30-157">The SignalRChat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="9fd30-158">它显示了如何创建一个中心。</span><span class="sxs-lookup"><span data-stu-id="9fd30-158">It shows you how to create a hub.</span></span> <span data-ttu-id="9fd30-159">服务器使用的主要协调对象为该中心。</span><span class="sxs-lookup"><span data-stu-id="9fd30-159">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="9fd30-160">在中心使用 SignalR jQuery 库来发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="9fd30-160">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="9fd30-161">SignalR 集线器中 ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="9fd30-161">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="9fd30-162">在上面的代码示例`ChatHub`类派生自`Microsoft.AspNet.SignalR.Hub`类。</span><span class="sxs-lookup"><span data-stu-id="9fd30-162">In the code sample above, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="9fd30-163">派生自`Hub`类是一种有用的方式来构建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9fd30-163">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="9fd30-164">可以在中心类上创建的公共方法，然后通过调用从网页中的脚本中使用这些方法。</span><span class="sxs-lookup"><span data-stu-id="9fd30-164">You can create public methods on your hub class and then use those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="9fd30-165">在聊天代码中，客户端调用`ChatHub.Send`方法发送一封新邮件。</span><span class="sxs-lookup"><span data-stu-id="9fd30-165">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="9fd30-166">在中心然后将消息发送到所有客户端，通过调用`Clients.All.broadcastMessage`。</span><span class="sxs-lookup"><span data-stu-id="9fd30-166">The hub then sends the message to all clients by calling `Clients.All.broadcastMessage`.</span></span>

<span data-ttu-id="9fd30-167">`Send`方法演示了多个中心概念：</span><span class="sxs-lookup"><span data-stu-id="9fd30-167">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="9fd30-168">集线器上声明的公共方法，以便客户端可以调用它们。</span><span class="sxs-lookup"><span data-stu-id="9fd30-168">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="9fd30-169">使用`Microsoft.AspNet.SignalR.Hub.Clients`与所有客户端通信的动态属性连接到此中心。</span><span class="sxs-lookup"><span data-stu-id="9fd30-169">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="9fd30-170">在客户端上调用的函数 (如`broadcastMessage`函数) 来更新客户端。</span><span class="sxs-lookup"><span data-stu-id="9fd30-170">Call a function on the client (like the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a><span data-ttu-id="9fd30-171">SignalR 和 jQuery 在 index.html 中</span><span class="sxs-lookup"><span data-stu-id="9fd30-171">SignalR and jQuery in the index.html</span></span>

<span data-ttu-id="9fd30-172">*Index.html*页中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。</span><span class="sxs-lookup"><span data-stu-id="9fd30-172">The *index.html* page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="9fd30-173">代码执行许多重要任务。</span><span class="sxs-lookup"><span data-stu-id="9fd30-173">The code carries out many important tasks.</span></span> <span data-ttu-id="9fd30-174">它声明的代理服务器能够引用中心，声明了函数内容推送到客户端，可以调用在服务器和它的起始位置将消息发送到中心的连接。</span><span class="sxs-lookup"><span data-stu-id="9fd30-174">It declares a proxy to reference the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> <span data-ttu-id="9fd30-175">在 JavaScript 中于服务器类及其成员的引用必须是驼峰式大小写。</span><span class="sxs-lookup"><span data-stu-id="9fd30-175">In JavaScript the reference to the server class and its members has to be camelCase.</span></span> <span data-ttu-id="9fd30-176">代码示例引用C# *ChatHub*中作为 JavaScript 类`chatHub`。</span><span class="sxs-lookup"><span data-stu-id="9fd30-176">The code sample references the C# *ChatHub* class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="9fd30-177">在此代码块中，创建脚本中的回调函数。</span><span class="sxs-lookup"><span data-stu-id="9fd30-177">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

<span data-ttu-id="9fd30-178">在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。</span><span class="sxs-lookup"><span data-stu-id="9fd30-178">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="9fd30-179">两个行，进行 HTML 编码的内容之前显示它是可选的显示阻止脚本注入的好方法。</span><span class="sxs-lookup"><span data-stu-id="9fd30-179">The two lines that HTML-encode the content before displaying it are optional and show a good way to prevent script injection.</span></span>

<span data-ttu-id="9fd30-180">此代码打开到中心的连接。</span><span class="sxs-lookup"><span data-stu-id="9fd30-180">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> <span data-ttu-id="9fd30-181">此方法可确保代码建立的连接，事件处理程序在执行之前。</span><span class="sxs-lookup"><span data-stu-id="9fd30-181">This approach ensures that the code establishes a connection before the event handler executes.</span></span>

<span data-ttu-id="9fd30-182">代码启动连接，然后将其传递函数来处理单击事件上**发送**HTML 页中的按钮。</span><span class="sxs-lookup"><span data-stu-id="9fd30-182">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="9fd30-183">获取代码</span><span class="sxs-lookup"><span data-stu-id="9fd30-183">Get the code</span></span>

[<span data-ttu-id="9fd30-184">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="9fd30-184">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)

## <a name="additional-resources"></a><span data-ttu-id="9fd30-185">其他资源</span><span class="sxs-lookup"><span data-stu-id="9fd30-185">Additional resources</span></span>

<span data-ttu-id="9fd30-186">有关 SignalR 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="9fd30-186">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="9fd30-187">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="9fd30-187">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="9fd30-188">SignalR Github 和示例</span><span class="sxs-lookup"><span data-stu-id="9fd30-188">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="9fd30-189">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="9fd30-189">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="9fd30-190">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9fd30-190">Next steps</span></span>

<span data-ttu-id="9fd30-191">在本教程中你：</span><span class="sxs-lookup"><span data-stu-id="9fd30-191">In this tutorial you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9fd30-192">设置项目</span><span class="sxs-lookup"><span data-stu-id="9fd30-192">Set up the project</span></span>
> * <span data-ttu-id="9fd30-193">运行示例</span><span class="sxs-lookup"><span data-stu-id="9fd30-193">Ran the sample</span></span>
> * <span data-ttu-id="9fd30-194">检查代码</span><span class="sxs-lookup"><span data-stu-id="9fd30-194">Examined the code</span></span>

<span data-ttu-id="9fd30-195">转到下一步的文章，了解如何使用 SignalR 和 MVC 5。</span><span class="sxs-lookup"><span data-stu-id="9fd30-195">Advance to the next article to learn how to use SignalR and MVC 5.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="9fd30-196">SignalR 2 和 MVC 5</span><span class="sxs-lookup"><span data-stu-id="9fd30-196">SignalR 2 and MVC 5</span></span>](tutorial-getting-started-with-signalr-and-mvc.md)