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
<a name="tutorial-getting-started-with-signalr-1x-and-mvc-4"></a><span data-ttu-id="eaace-103">教程：开始使用 SignalR 1.x 和 MVC 4</span><span class="sxs-lookup"><span data-stu-id="eaace-103">Tutorial: Getting Started with SignalR 1.x and MVC 4</span></span>
====================
<span data-ttu-id="eaace-104">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="eaace-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="eaace-105">本教程演示如何使用 ASP.NET SignalR 创建实时聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-105">This tutorial shows how to use ASP.NET SignalR to create a real-time chat application.</span></span> <span data-ttu-id="eaace-106">将将 SignalR 添加到 MVC 4 应用程序，并创建要发送，并显示消息的聊天视图。</span><span class="sxs-lookup"><span data-stu-id="eaace-106">You will add SignalR to an MVC 4 application and create a chat view to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="eaace-107">概述</span><span class="sxs-lookup"><span data-stu-id="eaace-107">Overview</span></span>

<span data-ttu-id="eaace-108">本教程向您介绍使用 ASP.NET SignalR 和 ASP.NET MVC 4 的实时 web 应用程序开发。</span><span class="sxs-lookup"><span data-stu-id="eaace-108">This tutorial introduces you to real-time web application development with ASP.NET SignalR and ASP.NET MVC 4.</span></span> <span data-ttu-id="eaace-109">本教程使用相同的聊天应用程序代码作为[SignalR 入门教程](tutorial-getting-started-with-signalr.md)，但显示了如何将其添加到 MVC 4 应用程序在基于 Internet 的模板。</span><span class="sxs-lookup"><span data-stu-id="eaace-109">The tutorial uses the same chat application code as the [SignalR Getting Started tutorial](tutorial-getting-started-with-signalr.md), but shows how to add it to an MVC 4 application based on the Internet template.</span></span>

<span data-ttu-id="eaace-110">在本主题中，您将学习以下的 SignalR 开发任务：</span><span class="sxs-lookup"><span data-stu-id="eaace-110">In this topic you will learn the following SignalR development tasks:</span></span>

- <span data-ttu-id="eaace-111">SignalR 库添加到 MVC 4 应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-111">Adding the SignalR library to an MVC 4 application.</span></span>
- <span data-ttu-id="eaace-112">创建用于将内容推送到客户端的中心类。</span><span class="sxs-lookup"><span data-stu-id="eaace-112">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="eaace-113">使用 SignalR jQuery 库在网页上发送消息并显示从中心的更新。</span><span class="sxs-lookup"><span data-stu-id="eaace-113">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="eaace-114">以下屏幕截图显示在浏览器中运行的已完成的聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-114">The following screen shot shows the completed chat application running in a browser.</span></span>

![聊天实例](tutorial-getting-started-with-signalr-and-mvc-4/_static/image2.png)

<span data-ttu-id="eaace-116">部分：</span><span class="sxs-lookup"><span data-stu-id="eaace-116">Sections:</span></span>

- [<span data-ttu-id="eaace-117">设置项目</span><span class="sxs-lookup"><span data-stu-id="eaace-117">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="eaace-118">运行示例</span><span class="sxs-lookup"><span data-stu-id="eaace-118">Run the Sample</span></span>](#run)
- [<span data-ttu-id="eaace-119">检查代码</span><span class="sxs-lookup"><span data-stu-id="eaace-119">Examine the Code</span></span>](#code)
- [<span data-ttu-id="eaace-120">后续步骤</span><span class="sxs-lookup"><span data-stu-id="eaace-120">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="eaace-121">设置项目</span><span class="sxs-lookup"><span data-stu-id="eaace-121">Set up the Project</span></span>

<span data-ttu-id="eaace-122">先决条件：</span><span class="sxs-lookup"><span data-stu-id="eaace-122">Prerequisites:</span></span>

- <span data-ttu-id="eaace-123">Visual Studio 2010 SP1 中，Visual Studio 2012 或 Visual Studio 2012 Express。</span><span class="sxs-lookup"><span data-stu-id="eaace-123">Visual Studio 2010 SP1, Visual Studio 2012, or Visual Studio 2012 Express.</span></span> <span data-ttu-id="eaace-124">如果未安装 Visual Studio，请参阅[ASP.NET 下载](https://www.asp.net/downloads)若要获取免费 Visual Studio 2012 Express 的开发工具。</span><span class="sxs-lookup"><span data-stu-id="eaace-124">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="eaace-125">对于 Visual Studio 2010，安装[ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683)。</span><span class="sxs-lookup"><span data-stu-id="eaace-125">For Visual Studio 2010, install [ASP.NET MVC 4](https://www.microsoft.com/download/details.aspx?id=30683).</span></span>

<span data-ttu-id="eaace-126">本部分演示如何创建 ASP.NET MVC 4 应用程序、 添加 SignalR 库，并创建聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-126">This section shows how to create an ASP.NET MVC 4 application, add the SignalR library, and create the chat application.</span></span>

1. 1. <span data-ttu-id="eaace-127">在 Visual Studio 创建 ASP.NET MVC 4 应用程序，其命名为 SignalRChat，并单击确定。</span><span class="sxs-lookup"><span data-stu-id="eaace-127">In Visual Studio create an ASP.NET MVC 4 application, name it SignalRChat, and click OK.</span></span>

        > [!NOTE]
        > <span data-ttu-id="eaace-128">在 VS 2010 中，选择 **.NET Framework 4** Framework 版本下拉列表控件中。</span><span class="sxs-lookup"><span data-stu-id="eaace-128">In VS 2010, select **.NET Framework 4** in the Framework version dropdown control.</span></span> <span data-ttu-id="eaace-129">SignalR 代码在.NET Framework 版本 4 和 4.5 上运行。</span><span class="sxs-lookup"><span data-stu-id="eaace-129">SignalR code runs on .NET Framework versions 4 and 4.5.</span></span>

        ![创建 mvc web](tutorial-getting-started-with-signalr-and-mvc-4/_static/image3.png)
      2. <span data-ttu-id="eaace-131">选择 Internet 应用程序模板，请清除选项**创建单元测试项目**，单击确定。</span><span class="sxs-lookup"><span data-stu-id="eaace-131">Select the Internet Application template, clear the option to **Create a unit test project**, and click OK.</span></span>

         ![创建 mvc internet 站点](tutorial-getting-started-with-signalr-and-mvc-4/_static/image4.png)
      3. <span data-ttu-id="eaace-133">打开**工具 > NuGet 包管理器 > 程序包管理器控制台**并运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="eaace-133">Open the **Tools > NuGet Package Manager > Package Manager Console** and run the following command.</span></span> <span data-ttu-id="eaace-134">此步骤将一组脚本文件和启用 SignalR 功能的程序集引用添加到项目。</span><span class="sxs-lookup"><span data-stu-id="eaace-134">This step adds to the project a set of script files and assembly references that enable SignalR functionality.</span></span>

         `install-package Microsoft.AspNet.SignalR -Version 1.1.3`
      4. <span data-ttu-id="eaace-135">在中**解决方案资源管理器**展开脚本文件夹。</span><span class="sxs-lookup"><span data-stu-id="eaace-135">In **Solution Explorer** expand the Scripts folder.</span></span> <span data-ttu-id="eaace-136">请注意有关 SignalR 的脚本库已添加到项目。</span><span class="sxs-lookup"><span data-stu-id="eaace-136">Note that script libraries for SignalR have been added to the project.</span></span>

         ![库引用](tutorial-getting-started-with-signalr-and-mvc-4/_static/image6.png)
      5. <span data-ttu-id="eaace-138">在中**解决方案资源管理器**，右键单击项目，选择**添加 |新的文件夹**，并添加名为的新文件夹**中心**。</span><span class="sxs-lookup"><span data-stu-id="eaace-138">In **Solution Explorer**, right-click the project, select **Add | New Folder**, and add a new folder named **Hubs**.</span></span>
      6. <span data-ttu-id="eaace-139">右键单击**中心**文件夹中，单击**添加 |类**，并创建一个新 C# 类名为**ChatHub.cs**。</span><span class="sxs-lookup"><span data-stu-id="eaace-139">Right-click the **Hubs** folder, click **Add | Class**, and create a new C# class named **ChatHub.cs**.</span></span> <span data-ttu-id="eaace-140">将此类用作 SignalR 服务器集线器将消息发送到所有客户端。</span><span class="sxs-lookup"><span data-stu-id="eaace-140">You will use this class as a SignalR server hub that sends messages to all clients.</span></span>

> [!NOTE]
> <span data-ttu-id="eaace-141">如果你使用 Visual Studio 2012 并已安装[ASP.NET 和 Web Tools 2012.2 更新](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation)，可以使用新的 SignalR 项模板创建 hub 类。</span><span class="sxs-lookup"><span data-stu-id="eaace-141">If you use Visual Studio 2012 and have installed the [ASP.NET and Web Tools 2012.2 update](../../../visual-studio/overview/2012/aspnet-and-web-tools-20122-release-notes-rtw.md#_Installation), you can use the new SignalR item template to create the hub class.</span></span> <span data-ttu-id="eaace-142">为此，请右键单击**中心**文件夹中，单击**添加 |新项**，选择**SignalR Hub 类 (v1)**，并将命名类**ChatHub.cs**。</span><span class="sxs-lookup"><span data-stu-id="eaace-142">To do that, right-click the **Hubs** folder, click **Add | New Item**, select **SignalR Hub Class (v1)**, and name the class **ChatHub.cs**.</span></span>


1. <span data-ttu-id="eaace-143">中的代码替换**ChatHub**用下面的代码的类。</span><span class="sxs-lookup"><span data-stu-id="eaace-143">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample1.cs)]
2. <span data-ttu-id="eaace-144">打开**Global.asax**文件的项目，并添加对方法的调用`RouteTable.Routes.MapHubs();`中的代码的第一行作为`Application_Start`方法。</span><span class="sxs-lookup"><span data-stu-id="eaace-144">Open the **Global.asax** file for the project, and add a call to the method `RouteTable.Routes.MapHubs();` as the first line of code in the `Application_Start` method.</span></span> <span data-ttu-id="eaace-145">此代码注册 SignalR 集线器的默认路由，必须在调用之前注册的任何其他路由。</span><span class="sxs-lookup"><span data-stu-id="eaace-145">This code registers the default route for SignalR hubs and must be called before you register any other routes.</span></span> <span data-ttu-id="eaace-146">已完成`Application_Start`方法看起来如下例所示。</span><span class="sxs-lookup"><span data-stu-id="eaace-146">The completed `Application_Start` method looks like the following example.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample2.cs)]
3. <span data-ttu-id="eaace-147">编辑`HomeController`类中找到**controllers/Homecontroller.cs**并将以下方法添加到类。</span><span class="sxs-lookup"><span data-stu-id="eaace-147">Edit the `HomeController` class found in **Controllers/HomeController.cs** and add the following method to the class.</span></span> <span data-ttu-id="eaace-148">此方法返回**聊天**将在稍后的步骤中创建的视图。</span><span class="sxs-lookup"><span data-stu-id="eaace-148">This method returns the **Chat** view that you will create in a later step.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample3.cs)]
4. <span data-ttu-id="eaace-149">在中单击右键`Chat`方法只需创建，然后单击**添加视图**创建新的视图文件。</span><span class="sxs-lookup"><span data-stu-id="eaace-149">Right-click within the `Chat` method you just created, and click **Add View** to create a new view file.</span></span>
5. <span data-ttu-id="eaace-150">在**添加视图**对话框中，请确保为选中此复选框**使用布局或母版页页**（清除其他复选框），然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="eaace-150">In the **Add View** dialog, make sure the check box is selected to **Use a layout or master page** (clear the other check boxes), and then click **Add**.</span></span>

    ![添加视图](tutorial-getting-started-with-signalr-and-mvc-4/_static/image8.png)
6. <span data-ttu-id="eaace-152">编辑名为的新视图文件**Chat.cshtml**。</span><span class="sxs-lookup"><span data-stu-id="eaace-152">Edit the new view file named **Chat.cshtml**.</span></span> <span data-ttu-id="eaace-153">之后&lt;h2&gt;标记中，粘贴以下&lt;div&gt;部分和`@section scripts`到页的代码块。</span><span class="sxs-lookup"><span data-stu-id="eaace-153">After the &lt;h2&gt; tag, paste the following &lt;div&gt; section and `@section scripts` code block into the page.</span></span> <span data-ttu-id="eaace-154">此脚本可将页后，可以发送聊天消息，并显示来自服务器的消息。</span><span class="sxs-lookup"><span data-stu-id="eaace-154">This script enables the page to send chat messages and display messages from the server.</span></span> <span data-ttu-id="eaace-155">下面的代码块中出现聊天视图的完整代码。</span><span class="sxs-lookup"><span data-stu-id="eaace-155">The complete code for the chat view appears in the following code block.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="eaace-156">在您将 SignalR 和其他脚本库添加到你的 Visual Studio 项目中，包管理器可能安装版本比本主题中所示的版本较新的脚本。</span><span class="sxs-lookup"><span data-stu-id="eaace-156">When you add SignalR and other script libraries to your Visual Studio project, the Package Manager might install versions of the scripts that are more recent than the versions shown in this topic.</span></span> <span data-ttu-id="eaace-157">请确保你的代码中的脚本引用与项目中安装的脚本库的版本相匹配。</span><span class="sxs-lookup"><span data-stu-id="eaace-157">Make sure that the script references in your code match the versions of the script libraries installed in your project.</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample4.cshtml)]
7. <span data-ttu-id="eaace-158">**保存所有**项目。</span><span class="sxs-lookup"><span data-stu-id="eaace-158">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="eaace-159">运行示例</span><span class="sxs-lookup"><span data-stu-id="eaace-159">Run the Sample</span></span>

1. <span data-ttu-id="eaace-160">按 F5 以在调试模式下运行该项目。</span><span class="sxs-lookup"><span data-stu-id="eaace-160">Press F5 to run the project in debug mode.</span></span>
2. <span data-ttu-id="eaace-161">在浏览器地址行中，追加 **/home/聊天**到项目的默认页的 URL。</span><span class="sxs-lookup"><span data-stu-id="eaace-161">In the browser address line, append **/home/chat** to the URL of the default page for the project.</span></span> <span data-ttu-id="eaace-162">聊天页面加载中的浏览器实例并提示输入用户名称。</span><span class="sxs-lookup"><span data-stu-id="eaace-162">The Chat page loads in a browser instance and prompts for a user name.</span></span>

    ![输入用户名](tutorial-getting-started-with-signalr-and-mvc-4/_static/image9.png)
3. <span data-ttu-id="eaace-164">输入用户名称。</span><span class="sxs-lookup"><span data-stu-id="eaace-164">Enter a user name.</span></span>
4. <span data-ttu-id="eaace-165">从地址行中的浏览器复制 URL 并将其用于打开两个更多的浏览器实例。</span><span class="sxs-lookup"><span data-stu-id="eaace-165">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="eaace-166">在每个浏览器实例中，输入唯一的用户名称。</span><span class="sxs-lookup"><span data-stu-id="eaace-166">In each browser instance, enter a unique user name.</span></span>
5. <span data-ttu-id="eaace-167">在每个浏览器实例中，添加注释并单击**发送**。</span><span class="sxs-lookup"><span data-stu-id="eaace-167">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="eaace-168">注释应显示在浏览器的所有实例。</span><span class="sxs-lookup"><span data-stu-id="eaace-168">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="eaace-169">此简单的聊天应用程序不维护服务器上的讨论上下文。</span><span class="sxs-lookup"><span data-stu-id="eaace-169">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="eaace-170">在中心广播到所有当前用户的注释。</span><span class="sxs-lookup"><span data-stu-id="eaace-170">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="eaace-171">加入聊天更高版本的用户将看到消息从时添加它们加入。</span><span class="sxs-lookup"><span data-stu-id="eaace-171">Users who join the chat later will see messages added from the time they join.</span></span>
6. <span data-ttu-id="eaace-172">以下屏幕截图显示在浏览器中运行的聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-172">The following screen shot shows the chat application running in a browser.</span></span>

    ![聊天浏览器](tutorial-getting-started-with-signalr-and-mvc-4/_static/image11.png)
7. <span data-ttu-id="eaace-174">在中**解决方案资源管理器**，检查**脚本文档**节点运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-174">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="eaace-175">如果使用 Internet Explorer 作为你的浏览器，此节点会显示在调试模式下。</span><span class="sxs-lookup"><span data-stu-id="eaace-175">This node is visible in debug mode if you are using Internet Explorer as your browser.</span></span> <span data-ttu-id="eaace-176">没有名为的脚本文件**中心**在运行时动态生成 SignalR 库。</span><span class="sxs-lookup"><span data-stu-id="eaace-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="eaace-177">此文件管理的 jQuery 脚本和服务器端代码之间的通信。</span><span class="sxs-lookup"><span data-stu-id="eaace-177">This file manages the communication between jQuery script and server-side code.</span></span> <span data-ttu-id="eaace-178">如果使用 Internet Explorer 以外的浏览器，您还可以访问动态**中心**通过浏览到它直接，例如文件 http://mywebsite/signalr/hubs。</span><span class="sxs-lookup"><span data-stu-id="eaace-178">If you use a browser other than Internet Explorer, you can also access the dynamic **hubs** file by browsing to it directly, for example http://mywebsite/signalr/hubs.</span></span>

    ![生成的中心脚本](tutorial-getting-started-with-signalr-and-mvc-4/_static/image13.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="eaace-180">检查代码</span><span class="sxs-lookup"><span data-stu-id="eaace-180">Examine the Code</span></span>

<span data-ttu-id="eaace-181">SignalR 聊天应用程序演示了两个基本的 SignalR 开发任务： 在服务器上的主要协调对象为创建中心和使用 SignalR jQuery 库来发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="eaace-181">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="eaace-182">SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="eaace-182">SignalR Hubs</span></span>

<span data-ttu-id="eaace-183">中的代码示例**ChatHub**类派生自**Microsoft.AspNet.SignalR.Hub**类。</span><span class="sxs-lookup"><span data-stu-id="eaace-183">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="eaace-184">派生自**中心**类是一种有用的方式来构建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-184">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="eaace-185">可以在中心类上创建的公共方法，然后通过调用从网页中的 jQuery 脚本来访问这些方法。</span><span class="sxs-lookup"><span data-stu-id="eaace-185">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="eaace-186">在聊天代码中，客户端调用**ChatHub.Send**方法发送一封新邮件。</span><span class="sxs-lookup"><span data-stu-id="eaace-186">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="eaace-187">在中心反过来将消息发送给所有客户端，通过调用**Clients.All.addNewMessageToPage**。</span><span class="sxs-lookup"><span data-stu-id="eaace-187">The hub in turn sends the message to all clients by calling **Clients.All.addNewMessageToPage**.</span></span>

<span data-ttu-id="eaace-188">**发送**方法演示了多个中心概念：</span><span class="sxs-lookup"><span data-stu-id="eaace-188">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="eaace-189">集线器上声明的公共方法，以便客户端可以调用它们。</span><span class="sxs-lookup"><span data-stu-id="eaace-189">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="eaace-190">使用**Microsoft.AspNet.SignalR.Hub.Clients**属性来访问所有客户端连接到此中心。</span><span class="sxs-lookup"><span data-stu-id="eaace-190">Use the **Microsoft.AspNet.SignalR.Hub.Clients** property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="eaace-191">在客户端上调用 jQuery 函数 (如`addNewMessageToPage`函数) 来更新客户端。</span><span class="sxs-lookup"><span data-stu-id="eaace-191">Call a jQuery function on the client (such as the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="eaace-192">SignalR 和 jQuery</span><span class="sxs-lookup"><span data-stu-id="eaace-192">SignalR and jQuery</span></span>

<span data-ttu-id="eaace-193">**Chat.cshtml**视图文件中的代码示例演示如何使用 SignalR jQuery 库与 SignalR 中心进行通信。</span><span class="sxs-lookup"><span data-stu-id="eaace-193">The **Chat.cshtml** view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="eaace-194">在代码中的基本任务创建对自动生成的代理为中心，声明函数的服务器可以调用内容推送到客户端，并启动连接来将消息发送到中心的引用。</span><span class="sxs-lookup"><span data-stu-id="eaace-194">The essential tasks in the code are creating a reference to the auto-generated proxy for the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="eaace-195">下面的代码声明集线器代理。</span><span class="sxs-lookup"><span data-stu-id="eaace-195">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="eaace-196">在 jQuery 中于服务器类及其成员的引用位于混合大小写。</span><span class="sxs-lookup"><span data-stu-id="eaace-196">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="eaace-197">代码示例将引用 C# **ChatHub**类中作为 jQuery **chatHub**。</span><span class="sxs-lookup"><span data-stu-id="eaace-197">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span> <span data-ttu-id="eaace-198">如果你想要引用`ChatHub`类在 jQuery 中使用传统的 Pascal 大小写，就像在 C# 中，编辑 ChatHub.cs 类文件。</span><span class="sxs-lookup"><span data-stu-id="eaace-198">If you want to reference the `ChatHub` class in jQuery with conventional Pascal casing as you would in C#, edit the ChatHub.cs class file.</span></span> <span data-ttu-id="eaace-199">添加`using`语句来引用`Microsoft.AspNet.SignalR.Hubs`命名空间。</span><span class="sxs-lookup"><span data-stu-id="eaace-199">Add a `using` statement to reference the `Microsoft.AspNet.SignalR.Hubs` namespace.</span></span> <span data-ttu-id="eaace-200">然后添加`HubName`归于`ChatHub`类，例如`[HubName("ChatHub")]`。</span><span class="sxs-lookup"><span data-stu-id="eaace-200">Then add the `HubName` attribute to the `ChatHub` class, for example `[HubName("ChatHub")]`.</span></span> <span data-ttu-id="eaace-201">最后，更新到你的 jQuery 引用`ChatHub`类。</span><span class="sxs-lookup"><span data-stu-id="eaace-201">Finally, update your jQuery reference to the `ChatHub` class.</span></span>


<span data-ttu-id="eaace-202">下面的代码演示如何在脚本中创建一个回调函数。</span><span class="sxs-lookup"><span data-stu-id="eaace-202">The following code shows how to create a callback function in the script.</span></span> <span data-ttu-id="eaace-203">在服务器上的中心类会调用此函数可将内容更新推送到每个客户端。</span><span class="sxs-lookup"><span data-stu-id="eaace-203">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="eaace-204">可选调用`htmlEncode`函数将显示一种方式，以 html 格式显示在页中，作为一种方法，以防止脚本注入之前编码消息内容。</span><span class="sxs-lookup"><span data-stu-id="eaace-204">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page, as a way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample7.html)]

<span data-ttu-id="eaace-205">下面的代码演示如何在中心打开的连接。</span><span class="sxs-lookup"><span data-stu-id="eaace-205">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="eaace-206">代码启动连接，然后将其传递函数来处理单击事件上**发送**聊天页中的按钮。</span><span class="sxs-lookup"><span data-stu-id="eaace-206">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

> [!NOTE]
> <span data-ttu-id="eaace-207">此方法可确保事件处理程序在执行之前建立的连接。</span><span class="sxs-lookup"><span data-stu-id="eaace-207">This approach ensures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc-4/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="eaace-208">后续步骤</span><span class="sxs-lookup"><span data-stu-id="eaace-208">Next Steps</span></span>

<span data-ttu-id="eaace-209">您学习了 SignalR 是一个框架，用于构建实时 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="eaace-209">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="eaace-210">您还学习了几个 SignalR 开发任务： 如何将 SignalR 添加到 ASP.NET 应用程序、 如何创建 hub 类以及如何发送和接收来自中心的消息。</span><span class="sxs-lookup"><span data-stu-id="eaace-210">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="eaace-211">若要了解更高级的 SignalR 开发概念，请访问以下站点 SignalR 源代码和资源：</span><span class="sxs-lookup"><span data-stu-id="eaace-211">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources :</span></span>

- [<span data-ttu-id="eaace-212">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="eaace-212">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="eaace-213">SignalR Github 和示例</span><span class="sxs-lookup"><span data-stu-id="eaace-213">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="eaace-214">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="eaace-214">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
