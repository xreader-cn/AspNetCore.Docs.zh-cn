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
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a><span data-ttu-id="6b4ac-104">教程：使用 SignalR 2 和 MVC 5 的实时聊天</span><span class="sxs-lookup"><span data-stu-id="6b4ac-104">Tutorial: Real-time chat with SignalR 2 and MVC 5</span></span>

<span data-ttu-id="6b4ac-105">本教程演示如何使用 ASP.NET SignalR 2 来创建实时聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-105">This tutorial shows how to use ASP.NET SignalR 2 to create a real-time chat application.</span></span> <span data-ttu-id="6b4ac-106">将 SignalR 添加到 MVC 5 应用程序并创建聊天视图以发送和显示的消息。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-106">You add SignalR to an MVC 5 application and create a chat view to send and display messages.</span></span>

<span data-ttu-id="6b4ac-107">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6b4ac-108">设置项目</span><span class="sxs-lookup"><span data-stu-id="6b4ac-108">Set up the project</span></span>
> * <span data-ttu-id="6b4ac-109">运行示例</span><span class="sxs-lookup"><span data-stu-id="6b4ac-109">Run the sample</span></span>
> * <span data-ttu-id="6b4ac-110">检查代码</span><span class="sxs-lookup"><span data-stu-id="6b4ac-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="6b4ac-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="6b4ac-111">Prerequisites</span></span>

* <span data-ttu-id="6b4ac-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="6b4ac-113">设置项目</span><span class="sxs-lookup"><span data-stu-id="6b4ac-113">Set up the Project</span></span>

<span data-ttu-id="6b4ac-114">本部分演示如何使用 Visual Studio 2017 和 SignalR 2 来创建一个空的 ASP.NET MVC 5 应用程序、 添加 SignalR 库，并创建聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET MVC 5 application, add the SignalR library, and create the chat application.</span></span>

1. <span data-ttu-id="6b4ac-115">在 Visual Studio 中，创建面向.NET Framework 4.5 的 C# ASP.NET 应用程序、 其命名为 SignalRChat，并单击确定。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-115">In Visual Studio, create a C# ASP.NET application that targets .NET Framework 4.5, name it SignalRChat, and click OK.</span></span>

    ![创建 web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. <span data-ttu-id="6b4ac-117">在中**新 ASP.NET Web 应用程序-SignalRMvcChat**，选择**MVC** ，然后选择**更改身份验证**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-117">In **New ASP.NET Web Application - SignalRMvcChat**, select **MVC** and then select **Change Authentication**.</span></span>

1. <span data-ttu-id="6b4ac-118">在中**更改身份验证**，选择**无身份验证**然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-118">In **Change Authentication**, select **No Authentication** and click **OK**.</span></span>

    ![选择无身份验证](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. <span data-ttu-id="6b4ac-120">在中**新 ASP.NET Web 应用程序-SignalRMvcChat**，选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-120">In **New ASP.NET Web Application - SignalRMvcChat**, select **OK**.</span></span>

1. <span data-ttu-id="6b4ac-121">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-121">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="6b4ac-122">在中**添加新项-SignalRChat**，选择**已安装** > **Visual C#**   >  **Web**  > **SignalR** ，然后选择**SignalR Hub 类 (v2)**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-122">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="6b4ac-123">将类命名*ChatHub*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-123">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="6b4ac-124">此步骤将创建*ChatHub.cs*类文件并添加一组脚本文件和 SignalR 支持到项目的程序集引用。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-124">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="6b4ac-125">在新的代码替换为*ChatHub.cs*类文件，此代码：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-125">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. <span data-ttu-id="6b4ac-126">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **类**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-126">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="6b4ac-127">将新类命名*启动*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-127">Name the new class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="6b4ac-128">中的代码替换*Startup.cs*类文件，此代码：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-128">Replace the code in the *Startup.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. <span data-ttu-id="6b4ac-129">在中**解决方案资源管理器**，选择**控制器** > **HomeController.cs**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-129">In **Solution Explorer**, select **Controllers** > **HomeController.cs**.</span></span>

1. <span data-ttu-id="6b4ac-130">将以下方法添加到*HomeController.cs*。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-130">Add this method to the *HomeController.cs*.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    <span data-ttu-id="6b4ac-131">此方法返回**聊天**在稍后的步骤中创建的视图。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-131">This method returns the **Chat** view that you create in a later step.</span></span>

1. <span data-ttu-id="6b4ac-132">在中**解决方案资源管理器**，右键单击**视图** > **主页**，然后选择**添加** >   **视图**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-132">In **Solution Explorer**, right-click **Views** > **Home**, and select **Add** >  **View**.</span></span>

1. <span data-ttu-id="6b4ac-133">在中**添加视图**，将新视图命名**聊天**，然后选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-133">In **Add View**, name the new view **Chat** and select **Add**.</span></span>

1. <span data-ttu-id="6b4ac-134">内容替换为**Chat.cshtml**使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-134">Replace the contents of **Chat.cshtml** with this code:</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. <span data-ttu-id="6b4ac-135">在中**解决方案资源管理器**，展开**脚本**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-135">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="6b4ac-136">适用于 jQuery 和 SignalR 的脚本库将显示在该项目。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-136">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6b4ac-137">包管理器可能已安装的 SignalR 脚本的更高版本。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-137">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="6b4ac-138">检查与项目中的脚本文件的版本相对应的代码块中的脚本引用。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-138">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="6b4ac-139">从原始的代码块的脚本引用：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-139">Script references from the original code block:</span></span>

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. <span data-ttu-id="6b4ac-140">如果不匹配，更新 *.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-140">If they don't match, update the *.cshtml* file.</span></span>

1. <span data-ttu-id="6b4ac-141">从菜单栏中，选择**文件** > **全部保存**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-141">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="6b4ac-142">运行示例</span><span class="sxs-lookup"><span data-stu-id="6b4ac-142">Run the Sample</span></span>

1. <span data-ttu-id="6b4ac-143">在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行示例。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-143">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![输入用户名](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. <span data-ttu-id="6b4ac-145">在浏览器打开时，输入你的聊天身份的名称。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-145">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="6b4ac-146">从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-146">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="6b4ac-147">在每个浏览器中，输入唯一名称。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-147">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="6b4ac-148">现在，添加注释并选择**发送**。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-148">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="6b4ac-149">在其他浏览器中重复的。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-149">Repeat that in the other browsers.</span></span> <span data-ttu-id="6b4ac-150">注释将显示在真实时间中。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-150">The comments appear in real time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6b4ac-151">此简单的聊天应用程序不维护服务器上的讨论上下文。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-151">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="6b4ac-152">在中心广播到所有当前用户的注释。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-152">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="6b4ac-153">加入聊天更高版本的用户将看到消息从时添加它们加入。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-153">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="6b4ac-154">请参阅聊天应用程序在三个不同的浏览器中的运行方式。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-154">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="6b4ac-155">当 Tom、 Anand 和 Susan 发送消息时，所有浏览器实时更新：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-155">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![所有三个浏览器显示相同的聊天历史记录](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. <span data-ttu-id="6b4ac-157">在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-157">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="6b4ac-158">没有名为的脚本文件*中心*SignalR 库生成在运行时。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-158">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="6b4ac-159">此文件管理的 jQuery 脚本和服务器端代码之间的通信。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-159">This file manages the communication between jQuery script and server-side code.</span></span>

    ![在脚本文档节点中自动生成中心脚本](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="6b4ac-161">检查代码</span><span class="sxs-lookup"><span data-stu-id="6b4ac-161">Examine the Code</span></span>

<span data-ttu-id="6b4ac-162">SignalR 聊天应用程序演示了两个基本的 SignalR 开发任务。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-162">The SignalR chat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="6b4ac-163">它显示了如何创建一个中心。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-163">It shows you how to create a hub.</span></span> <span data-ttu-id="6b4ac-164">服务器使用的主要协调对象为该中心。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-164">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="6b4ac-165">在中心使用 SignalR jQuery 库来发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-165">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="6b4ac-166">SignalR 集线器中 ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="6b4ac-166">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="6b4ac-167">在代码示例中，`ChatHub`类派生自`Microsoft.AspNet.SignalR.Hub`类。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-167">In the code sample, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="6b4ac-168">派生自`Hub`类是一种有用的方式来构建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-168">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="6b4ac-169">可以在中心类上创建的公共方法，然后通过调用从网页中的脚本中访问这些方法。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-169">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="6b4ac-170">在聊天代码中，客户端调用`ChatHub.Send`方法发送一封新邮件。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-170">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="6b4ac-171">在中心反过来将消息发送给所有客户端，通过调用`Clients.All.addNewMessageToPage`。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-171">The hub in turn sends the message to all clients by calling `Clients.All.addNewMessageToPage`.</span></span>

<span data-ttu-id="6b4ac-172">`Send`方法演示了多个中心概念：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-172">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="6b4ac-173">集线器上声明的公共方法，以便客户端可以调用它们。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-173">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="6b4ac-174">使用`Microsoft.AspNet.SignalR.Hub.Clients`与所有客户端通信的动态属性连接到此中心。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-174">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="6b4ac-175">在客户端上调用的函数 (如`addNewMessageToPage`函数) 来更新客户端。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-175">Call a function on the client (like the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a><span data-ttu-id="6b4ac-176">SignalR 和 jQuery Chat.cshtml</span><span class="sxs-lookup"><span data-stu-id="6b4ac-176">SignalR and jQuery Chat.cshtml</span></span>

<span data-ttu-id="6b4ac-177">*Chat.cshtml*视图文件中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-177">The *Chat.cshtml* view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span>  <span data-ttu-id="6b4ac-178">代码执行许多重要任务。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-178">The code carries out many important tasks.</span></span> <span data-ttu-id="6b4ac-179">它创建为中心的自动生成代理的引用，声明一个函数可以调用服务器可将内容推送到客户端，并开始将消息发送到中心的连接。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-179">It creates a reference to the autogenerated proxy for the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="6b4ac-180">在 JavaScript 中，于服务器类及其成员的引用位于驼峰式大小写。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-180">In JavaScript, the reference to the server class and its members is in camelCase.</span></span> <span data-ttu-id="6b4ac-181">代码示例引用C#`ChatHub`中作为 JavaScript 类`chatHub`。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-181">The code sample references the C# `ChatHub` class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="6b4ac-182">在此代码块中，创建脚本中的回调函数。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-182">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

<span data-ttu-id="6b4ac-183">在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-183">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="6b4ac-184">可选调用`htmlEncode`函数将显示一种方式，向 HTML 页中显示之前编码消息内容。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-184">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page.</span></span> <span data-ttu-id="6b4ac-185">它是一种方法，以防止脚本注入。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-185">It's a way to prevent script injection.</span></span>

<span data-ttu-id="6b4ac-186">此代码打开到中心的连接。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-186">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> <span data-ttu-id="6b4ac-187">此方法可确保事件处理程序在执行之前建立的连接。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-187">This approach ensures that you establish a connection before the event handler executes.</span></span>

<span data-ttu-id="6b4ac-188">代码启动连接，然后将其传递函数来处理单击事件上**发送**聊天页中的按钮。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-188">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6b4ac-189">获取代码</span><span class="sxs-lookup"><span data-stu-id="6b4ac-189">Get the code</span></span>

[<span data-ttu-id="6b4ac-190">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="6b4ac-190">Download Completed Project</span></span>](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

## <a name="additional-resources"></a><span data-ttu-id="6b4ac-191">其他资源</span><span class="sxs-lookup"><span data-stu-id="6b4ac-191">Additional resources</span></span>

<span data-ttu-id="6b4ac-192">有关 SignalR 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-192">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="6b4ac-193">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="6b4ac-193">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="6b4ac-194">SignalR GitHub 和示例</span><span class="sxs-lookup"><span data-stu-id="6b4ac-194">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="6b4ac-195">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="6b4ac-195">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="6b4ac-196">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6b4ac-196">Next steps</span></span>

<span data-ttu-id="6b4ac-197">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6b4ac-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6b4ac-198">设置项目</span><span class="sxs-lookup"><span data-stu-id="6b4ac-198">Set up the project</span></span>
> * <span data-ttu-id="6b4ac-199">运行示例</span><span class="sxs-lookup"><span data-stu-id="6b4ac-199">Ran the sample</span></span>
> * <span data-ttu-id="6b4ac-200">检查代码</span><span class="sxs-lookup"><span data-stu-id="6b4ac-200">Examined the code</span></span>

<span data-ttu-id="6b4ac-201">转到下一步的文章，了解如何创建使用 ASP.NET SignalR 2 提供高频率的消息传送功能的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b4ac-201">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="6b4ac-202">使用高频率消息传送的 web 应用</span><span class="sxs-lookup"><span data-stu-id="6b4ac-202">Web app with high-frequency messaging</span></span>](tutorial-high-frequency-realtime-with-signalr.md)