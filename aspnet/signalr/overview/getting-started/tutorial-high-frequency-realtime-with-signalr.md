---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 教程：使用 SignalR 2 创建高频率实时应用程序 |Microsoft Docs
author: bradygaster
description: 本教程演示如何创建使用 ASP.NET SignalR 来提供高频率消息传送功能的 web 应用程序。
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 44aaa2b0c059de310e963f642fa56c2f00a7e443
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836722"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a><span data-ttu-id="6def4-103">教程：使用 SignalR 2 创建高频率实时应用</span><span class="sxs-lookup"><span data-stu-id="6def4-103">Tutorial: Create high-frequency real-time app with SignalR 2</span></span>

<span data-ttu-id="6def4-104">本教程演示如何创建使用 ASP.NET SignalR 2 提供高频率的消息传送功能的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span> <span data-ttu-id="6def4-105">在这种情况下，"高频率消息传送"意味着服务器将更新发送以固定费率。</span><span class="sxs-lookup"><span data-stu-id="6def4-105">In this case, "high-frequency messaging" means the server sends updates at a fixed rate.</span></span> <span data-ttu-id="6def4-106">发送第二个最多 10 个消息。</span><span class="sxs-lookup"><span data-stu-id="6def4-106">You send up to 10 messages a second.</span></span>

<span data-ttu-id="6def4-107">您创建的应用程序显示用户可以将一个形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-107">The application you create displays a shape that users can drag.</span></span> <span data-ttu-id="6def4-108">服务器将更新该形状的所有连接的浏览器，以便与使用定时的更新拖动形状的位置中的位置。</span><span class="sxs-lookup"><span data-stu-id="6def4-108">The server updates the position of the shape in all connected browsers to match the position of the dragged shape using timed updates.</span></span>

<span data-ttu-id="6def4-109">本教程中介绍的概念有实时游戏中的应用程序和其他模拟应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-109">Concepts introduced in this tutorial have applications in real-time gaming and other simulation applications.</span></span>

<span data-ttu-id="6def4-110">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6def4-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6def4-111">设置项目</span><span class="sxs-lookup"><span data-stu-id="6def4-111">Set up the project</span></span>
> * <span data-ttu-id="6def4-112">创建基本应用程序</span><span class="sxs-lookup"><span data-stu-id="6def4-112">Create the base application</span></span>
> * <span data-ttu-id="6def4-113">当应用启动时将映射到中心</span><span class="sxs-lookup"><span data-stu-id="6def4-113">Map to the hub when app starts</span></span>
> * <span data-ttu-id="6def4-114">添加客户端</span><span class="sxs-lookup"><span data-stu-id="6def4-114">Add the client</span></span>
> * <span data-ttu-id="6def4-115">运行应用</span><span class="sxs-lookup"><span data-stu-id="6def4-115">Run the app</span></span>
> * <span data-ttu-id="6def4-116">添加客户端循环</span><span class="sxs-lookup"><span data-stu-id="6def4-116">Add the client loop</span></span>
> * <span data-ttu-id="6def4-117">添加服务器循环</span><span class="sxs-lookup"><span data-stu-id="6def4-117">Add the server loop</span></span>
> * <span data-ttu-id="6def4-118">添加流畅的动画</span><span class="sxs-lookup"><span data-stu-id="6def4-118">Add smooth animation</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="6def4-119">系统必备</span><span class="sxs-lookup"><span data-stu-id="6def4-119">Prerequisites</span></span>

* <span data-ttu-id="6def4-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="6def4-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="6def4-121">设置项目</span><span class="sxs-lookup"><span data-stu-id="6def4-121">Set up the project</span></span>

<span data-ttu-id="6def4-122">在本部分中，您将创建 Visual Studio 2017 中的项目。</span><span class="sxs-lookup"><span data-stu-id="6def4-122">In this section, you create the project in Visual Studio 2017.</span></span>

<span data-ttu-id="6def4-123">本部分演示如何使用 Visual Studio 2017 创建空的 ASP.NET Web 应用程序并将添加的 SignalR 和 jQuery.UI 库。</span><span class="sxs-lookup"><span data-stu-id="6def4-123">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application and add the SignalR and jQuery.UI libraries.</span></span>

1. <span data-ttu-id="6def4-124">在 Visual Studio 中创建 ASP.NET Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-124">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![创建 web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. <span data-ttu-id="6def4-126">在中**新 ASP.NET Web 应用程序-MoveShapeDemo**窗口中，保留**空**，并选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="6def4-126">In the **New ASP.NET Web Application - MoveShapeDemo** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="6def4-127">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="6def4-127">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="6def4-128">在中**添加新项-MoveShapeDemo**，选择**已安装** > **Visual C#**   >  **Web**  > **SignalR** ，然后选择**SignalR Hub 类 (v2)**。</span><span class="sxs-lookup"><span data-stu-id="6def4-128">In **Add New Item - MoveShapeDemo**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="6def4-129">将类命名*MoveShapeHub*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="6def4-129">Name the class *MoveShapeHub* and add it to the project.</span></span>

    <span data-ttu-id="6def4-130">此步骤将创建*MoveShapeHub.cs*类文件。</span><span class="sxs-lookup"><span data-stu-id="6def4-130">This step creates the *MoveShapeHub.cs* class file.</span></span> <span data-ttu-id="6def4-131">同时，它将添加一组脚本文件和 SignalR 支持到项目的程序集引用。</span><span class="sxs-lookup"><span data-stu-id="6def4-131">Simultaneously, it adds  a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="6def4-132">选择**工具** > **NuGet 包管理器** > **程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="6def4-132">Select **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

1. <span data-ttu-id="6def4-133">在中**程序包管理器控制台**，运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="6def4-133">In **Package Manager Console**, run this command:</span></span>

    ```console
    Install-Package jQuery.UI.Combined
    ```

    <span data-ttu-id="6def4-134">该命令将安装 jQuery UI 库。</span><span class="sxs-lookup"><span data-stu-id="6def4-134">The command installs the jQuery UI library.</span></span> <span data-ttu-id="6def4-135">您可以使用它来对形状进行动画处理。</span><span class="sxs-lookup"><span data-stu-id="6def4-135">You use it to animate the shape.</span></span>

1. <span data-ttu-id="6def4-136">在中**解决方案资源管理器**，展开脚本节点。</span><span class="sxs-lookup"><span data-stu-id="6def4-136">In **Solution Explorer**, expand the Scripts node.</span></span>

    ![脚本库引用](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    <span data-ttu-id="6def4-138">JQuery、 jQueryUI，和 SignalR 的脚本库将显示在该项目。</span><span class="sxs-lookup"><span data-stu-id="6def4-138">Script libraries for jQuery, jQueryUI, and SignalR are visible in the project.</span></span>

## <a name="create-the-base-application"></a><span data-ttu-id="6def4-139">创建基本应用程序</span><span class="sxs-lookup"><span data-stu-id="6def4-139">Create the base application</span></span>

<span data-ttu-id="6def4-140">在本部分中，您将创建浏览器应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-140">In this section, you create a browser application.</span></span> <span data-ttu-id="6def4-141">应用程序在每个鼠标移动事件向服务器发送形状的位置。</span><span class="sxs-lookup"><span data-stu-id="6def4-141">The app sends the location of the shape to the server during each mouse move event.</span></span> <span data-ttu-id="6def4-142">服务器会广播到所有其他连接的客户端在真实时间中的此信息。</span><span class="sxs-lookup"><span data-stu-id="6def4-142">The server broadcasts this information to all other connected clients in real time.</span></span> <span data-ttu-id="6def4-143">您了解有关此应用程序中后面部分的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6def4-143">You learn more about this application in later sections.</span></span>

1. <span data-ttu-id="6def4-144">打开*MoveShapeHub.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="6def4-144">Open the *MoveShapeHub.cs* file.</span></span>

1. <span data-ttu-id="6def4-145">中的代码替换*MoveShapeHub.cs*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6def4-145">Replace the code in the *MoveShapeHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="6def4-146">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="6def4-146">Save the file.</span></span>

<span data-ttu-id="6def4-147">`MoveShapeHub`类是实现 SignalR hub。</span><span class="sxs-lookup"><span data-stu-id="6def4-147">The `MoveShapeHub` class is an implementation of a SignalR hub.</span></span> <span data-ttu-id="6def4-148">在中作为[SignalR 入门](tutorial-getting-started-with-signalr.md)教程中，中心都有客户端直接调用的方法。</span><span class="sxs-lookup"><span data-stu-id="6def4-148">As in the [Getting Started with SignalR](tutorial-getting-started-with-signalr.md) tutorial, the hub has a method that the clients call directly.</span></span> <span data-ttu-id="6def4-149">在此情况下，客户端发送的对象使用新的 X 和 Y 坐标到服务器的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-149">In this case, the client sends an object with the new X and Y coordinates of the shape to the server.</span></span> <span data-ttu-id="6def4-150">这些坐标获取将广播到所有其他连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="6def4-150">Those coordinates get broadcasted to all other connected clients.</span></span> <span data-ttu-id="6def4-151">SignalR 自动序列化此对象使用 JSON。</span><span class="sxs-lookup"><span data-stu-id="6def4-151">SignalR automatically serializes this object using JSON.</span></span>

<span data-ttu-id="6def4-152">应用程序发送`ShapeModel`到客户端对象。</span><span class="sxs-lookup"><span data-stu-id="6def4-152">The app sends the `ShapeModel` object to the client.</span></span> <span data-ttu-id="6def4-153">它具有成员来存储该形状的位置。</span><span class="sxs-lookup"><span data-stu-id="6def4-153">It has members to store the position of the shape.</span></span> <span data-ttu-id="6def4-154">在服务器上对象的版本也有一个成员来跟踪哪些客户端的数据存储。</span><span class="sxs-lookup"><span data-stu-id="6def4-154">The version of the object on the server also has a member to track which client's data is being stored.</span></span> <span data-ttu-id="6def4-155">此对象可阻止服务器发送回发到自身的客户端的数据。</span><span class="sxs-lookup"><span data-stu-id="6def4-155">This object prevents the server from sending a client's data back to itself.</span></span> <span data-ttu-id="6def4-156">此成员使用`JsonIgnore`属性以防止序列化数据并将其发送回客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-156">This member uses the `JsonIgnore` attribute to keep the application from serializing the data and sending it back to the client.</span></span>

## <a name="map-to-the-hub-when-app-starts"></a><span data-ttu-id="6def4-157">当应用启动时将映射到中心</span><span class="sxs-lookup"><span data-stu-id="6def4-157">Map to the hub when app starts</span></span>

<span data-ttu-id="6def4-158">接下来，您设置映射到中心应用程序启动时。</span><span class="sxs-lookup"><span data-stu-id="6def4-158">Next, you set up mapping to the hub when the application starts.</span></span> <span data-ttu-id="6def4-159">在 SignalR 2 中，添加 OWIN 启动类创建的映射。</span><span class="sxs-lookup"><span data-stu-id="6def4-159">In SignalR 2, adding an OWIN startup class creates the mapping.</span></span>

1. <span data-ttu-id="6def4-160">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="6def4-160">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="6def4-161">在中**添加新项-MoveShapeDemo**选择**已安装** > **Visual C#**   >  **Web** ，然后选择**OWIN 启动类**。</span><span class="sxs-lookup"><span data-stu-id="6def4-161">In **Add New Item - MoveShapeDemo** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="6def4-162">将类命名*启动*，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="6def4-162">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="6def4-163">中的默认代码替换*Startup.cs*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6def4-163">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<span data-ttu-id="6def4-164">OWIN 启动类调用`MapSignalR`当应用程序执行`Configuration`方法。</span><span class="sxs-lookup"><span data-stu-id="6def4-164">The OWIN startup class calls `MapSignalR` when the app executes the `Configuration` method.</span></span> <span data-ttu-id="6def4-165">该应用将添加到 OWIN 的启动类处理使用`OwinStartup`程序集属性。</span><span class="sxs-lookup"><span data-stu-id="6def4-165">The app adds the class to OWIN's startup process using the `OwinStartup` assembly attribute.</span></span>

## <a name="add-the-client"></a><span data-ttu-id="6def4-166">添加客户端</span><span class="sxs-lookup"><span data-stu-id="6def4-166">Add the client</span></span>

<span data-ttu-id="6def4-167">添加客户端 HTML 页。</span><span class="sxs-lookup"><span data-stu-id="6def4-167">Add the HTML page for the client.</span></span>

1. <span data-ttu-id="6def4-168">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **HTML 页**。</span><span class="sxs-lookup"><span data-stu-id="6def4-168">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="6def4-169">将该页命名为**默认**，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="6def4-169">Name the page **Default** and select **OK**.</span></span>

1. <span data-ttu-id="6def4-170">在中**解决方案资源管理器**，右键单击*Default.html* ，然后选择**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="6def4-170">In **Solution Explorer**, right-click *Default.html* and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="6def4-171">中的默认代码替换*Default.html*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6def4-171">Replace the default code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. <span data-ttu-id="6def4-172">在中**解决方案资源管理器**，展开**脚本**。</span><span class="sxs-lookup"><span data-stu-id="6def4-172">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="6def4-173">适用于 jQuery 和 SignalR 的脚本库将显示在该项目。</span><span class="sxs-lookup"><span data-stu-id="6def4-173">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6def4-174">包管理器安装 SignalR 脚本的更高版本。</span><span class="sxs-lookup"><span data-stu-id="6def4-174">The package manager installs a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="6def4-175">更新要与项目中的脚本文件的版本相对应的代码块中的脚本引用。</span><span class="sxs-lookup"><span data-stu-id="6def4-175">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

<span data-ttu-id="6def4-176">此 HTML 和 JavaScript 代码将创建一个红色`div`调用`shape`。</span><span class="sxs-lookup"><span data-stu-id="6def4-176">This HTML and JavaScript code creates a red `div` called `shape`.</span></span> <span data-ttu-id="6def4-177">启用使用 jQuery 库的形状的拖放行为，并使用`drag`事件以向服务器发送形状的位置。</span><span class="sxs-lookup"><span data-stu-id="6def4-177">It enables the shape's dragging behavior using the jQuery library and uses the `drag` event to send the shape's position to the server.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6def4-178">运行应用</span><span class="sxs-lookup"><span data-stu-id="6def4-178">Run the app</span></span>

<span data-ttu-id="6def4-179">你可以运行该应用到 se'e 它起作用。</span><span class="sxs-lookup"><span data-stu-id="6def4-179">You can run the app to se\`e it work.</span></span> <span data-ttu-id="6def4-180">当浏览器窗口中将形状时，形状将在其他浏览器过。</span><span class="sxs-lookup"><span data-stu-id="6def4-180">When you drag the shape around a browser window, the shape moves in the other browsers too.</span></span>

1. <span data-ttu-id="6def4-181">在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-181">In the toolbar, turn on **Script Debugging** and then select the play button to run the application in Debug mode.</span></span>

    ![启用调试模式下和选择播放的用户的屏幕截图。](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    <span data-ttu-id="6def4-183">使用右上角中的红色形状会打开一个浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="6def4-183">A browser window opens with the red shape in the upper-right corner.</span></span>

1. <span data-ttu-id="6def4-184">复制页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="6def4-184">Copy the page's URL.</span></span>

1. <span data-ttu-id="6def4-185">打开另一个浏览器并将 URL 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="6def4-185">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="6def4-186">将一个浏览器窗口中的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-186">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="6def4-187">遵循其他浏览器窗口中的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-187">The shape in the other browser window follows.</span></span>

<span data-ttu-id="6def4-188">尽管应用程序使用此方法的函数，它不是建议的编程模型。</span><span class="sxs-lookup"><span data-stu-id="6def4-188">While the application functions using this method, it's not a recommended programming model.</span></span> <span data-ttu-id="6def4-189">获取发送的消息数没有上限。</span><span class="sxs-lookup"><span data-stu-id="6def4-189">There's no upper limit to the number of messages getting sent.</span></span> <span data-ttu-id="6def4-190">因此，客户端和服务器获取过重消息和性能下降。</span><span class="sxs-lookup"><span data-stu-id="6def4-190">As a result, the clients and server get overwhelmed with messages and performance degrades.</span></span> <span data-ttu-id="6def4-191">此外，应用程序在客户端上显示不相互连接的动画。</span><span class="sxs-lookup"><span data-stu-id="6def4-191">Also, the app displays a disjointed animation on the client.</span></span> <span data-ttu-id="6def4-192">形状的每个方法，从而立即移动会发生此显得很不稳定的动画。</span><span class="sxs-lookup"><span data-stu-id="6def4-192">This jerky animation happens because the shape moves instantly by each method.</span></span> <span data-ttu-id="6def4-193">如果该形状将顺利地移动到每个新的位置，则是更好。</span><span class="sxs-lookup"><span data-stu-id="6def4-193">It's better if the shape moves smoothly to each new location.</span></span> <span data-ttu-id="6def4-194">接下来，您将了解如何解决这些问题。</span><span class="sxs-lookup"><span data-stu-id="6def4-194">Next, you learn how to fix those issues.</span></span>

## <a name="add-the-client-loop"></a><span data-ttu-id="6def4-195">添加客户端循环</span><span class="sxs-lookup"><span data-stu-id="6def4-195">Add the client loop</span></span>

<span data-ttu-id="6def4-196">发送形状上每个鼠标移动事件的位置创建不必要的网络流量大小。</span><span class="sxs-lookup"><span data-stu-id="6def4-196">Sending the location of the shape on every mouse move event creates an unnecessary amount of network traffic.</span></span> <span data-ttu-id="6def4-197">应用需要来限制从客户端的消息。</span><span class="sxs-lookup"><span data-stu-id="6def4-197">The app needs to throttle the messages from the client.</span></span>

<span data-ttu-id="6def4-198">使用 javascript`setInterval`函数来设置一个循环，将新的位置信息发送到服务器以固定费率。</span><span class="sxs-lookup"><span data-stu-id="6def4-198">Use the javascript `setInterval` function to set up a loop that sends new position information to the server at a fixed rate.</span></span> <span data-ttu-id="6def4-199">此循环是基本表示形式的"游戏循环"。</span><span class="sxs-lookup"><span data-stu-id="6def4-199">This loop is a basic representation of a "game loop."</span></span> <span data-ttu-id="6def4-200">它是游戏的重复调用的函数的驱动器的所有功能。</span><span class="sxs-lookup"><span data-stu-id="6def4-200">It's a repeatedly called function that drives all the functionality of a game.</span></span>

1. <span data-ttu-id="6def4-201">中的客户端代码替换*Default.html*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6def4-201">Replace the client code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > <span data-ttu-id="6def4-202">您必须再次将脚本引用。</span><span class="sxs-lookup"><span data-stu-id="6def4-202">You have to replace the script references again.</span></span> <span data-ttu-id="6def4-203">它们必须匹配版本的项目中的脚本。</span><span class="sxs-lookup"><span data-stu-id="6def4-203">They must match the versions of the scripts in the project.</span></span>

    <span data-ttu-id="6def4-204">此新代码将添加`updateServerModel`函数。</span><span class="sxs-lookup"><span data-stu-id="6def4-204">This new code adds the `updateServerModel` function.</span></span> <span data-ttu-id="6def4-205">固定频率获取调用它。</span><span class="sxs-lookup"><span data-stu-id="6def4-205">It gets called on a fixed frequency.</span></span> <span data-ttu-id="6def4-206">该函数将位置数据发送到服务器时`moved`标志指示是要发送的新位置数据。</span><span class="sxs-lookup"><span data-stu-id="6def4-206">The function sends the position data to the server whenever the `moved` flag indicates that there's new position data to send.</span></span>

1. <span data-ttu-id="6def4-207">选择播放按钮启动应用程序</span><span class="sxs-lookup"><span data-stu-id="6def4-207">Select the play button to start the application</span></span>

1. <span data-ttu-id="6def4-208">复制页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="6def4-208">Copy the page's URL.</span></span>

1. <span data-ttu-id="6def4-209">打开另一个浏览器并将 URL 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="6def4-209">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="6def4-210">将一个浏览器窗口中的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-210">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="6def4-211">遵循其他浏览器窗口中的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-211">The shape in the other browser window follows.</span></span>

<span data-ttu-id="6def4-212">由于应用程序限制到服务器，动画将不会出现顺利地获取发送的消息数未在第一个。</span><span class="sxs-lookup"><span data-stu-id="6def4-212">Since the app throttles the number of messages that get sent to the server, the animation won't appear as smooth did at first.</span></span>

## <a name="add-the-server-loop"></a><span data-ttu-id="6def4-213">添加服务器循环</span><span class="sxs-lookup"><span data-stu-id="6def4-213">Add the server loop</span></span>

<span data-ttu-id="6def4-214">在当前应用程序，从服务器发送到客户端的消息转通常在接收到。</span><span class="sxs-lookup"><span data-stu-id="6def4-214">In the current application, messages sent from the server to the client go out as often as they're received.</span></span> <span data-ttu-id="6def4-215">正如我们看到客户端上，此网络流量提供了类似的问题。</span><span class="sxs-lookup"><span data-stu-id="6def4-215">This network traffic presents a similar problem as we see on the client.</span></span>

<span data-ttu-id="6def4-216">应用程序可以不是在需要更频繁地发送消息。</span><span class="sxs-lookup"><span data-stu-id="6def4-216">The app can send messages more often than they're needed.</span></span> <span data-ttu-id="6def4-217">连接可能会变得来路作为结果。</span><span class="sxs-lookup"><span data-stu-id="6def4-217">The connection can become flooded as a result.</span></span> <span data-ttu-id="6def4-218">本部分介绍如何更新服务器，以添加一个计时器，用于限制的传出消息的速率。</span><span class="sxs-lookup"><span data-stu-id="6def4-218">This section describes how to update the server to add a timer that throttles the rate of the outgoing messages.</span></span>

1. <span data-ttu-id="6def4-219">内容替换为`MoveShapeHub.cs`使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="6def4-219">Replace the contents of `MoveShapeHub.cs` with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. <span data-ttu-id="6def4-220">选择播放按钮启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-220">Select the play button to start the application.</span></span>

1. <span data-ttu-id="6def4-221">复制页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="6def4-221">Copy the page's URL.</span></span>

1. <span data-ttu-id="6def4-222">打开另一个浏览器并将 URL 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="6def4-222">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="6def4-223">将一个浏览器窗口中的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-223">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="6def4-224">此代码扩展客户端添加`Broadcaster`类。</span><span class="sxs-lookup"><span data-stu-id="6def4-224">This code expands the client to add the `Broadcaster` class.</span></span> <span data-ttu-id="6def4-225">新类将限制传出消息使用`Timer`从.NET framework 类。</span><span class="sxs-lookup"><span data-stu-id="6def4-225">The new class throttles the outgoing messages using the `Timer` class from the .NET framework.</span></span>

<span data-ttu-id="6def4-226">若要了解中心本身是暂时性适合。</span><span class="sxs-lookup"><span data-stu-id="6def4-226">It's good to learn that the hub itself is transitory.</span></span> <span data-ttu-id="6def4-227">每次在需要时，将创建它。</span><span class="sxs-lookup"><span data-stu-id="6def4-227">It's created every time it's needed.</span></span> <span data-ttu-id="6def4-228">因此，应用程序创建`Broadcaster`作为单一实例。</span><span class="sxs-lookup"><span data-stu-id="6def4-228">So the app creates the `Broadcaster` as a singleton.</span></span> <span data-ttu-id="6def4-229">它使用延迟初始化来延迟`Broadcaster`的需要时创建。</span><span class="sxs-lookup"><span data-stu-id="6def4-229">It uses lazy initialization to defer the `Broadcaster`'s creation until it's needed.</span></span> <span data-ttu-id="6def4-230">这样可确保，应用程序之前已创建的第一个集线器实例完全启动计时器。</span><span class="sxs-lookup"><span data-stu-id="6def4-230">That guarantees that the app creates the first hub instance completely before starting the timer.</span></span>

<span data-ttu-id="6def4-231">对客户端的调用`UpdateShape`函数然后移出该中心的`UpdateModel`方法。</span><span class="sxs-lookup"><span data-stu-id="6def4-231">The call to the clients' `UpdateShape` function is then moved out of the hub's `UpdateModel` method.</span></span> <span data-ttu-id="6def4-232">不能再调用应用程序将收到传入消息时，立即。</span><span class="sxs-lookup"><span data-stu-id="6def4-232">It's no longer called immediately whenever the app receives incoming messages.</span></span> <span data-ttu-id="6def4-233">相反，应用程序将消息发送到客户端每秒 25 调用的速率。</span><span class="sxs-lookup"><span data-stu-id="6def4-233">Instead, the app sends the messages to the clients at a rate of 25 calls per second.</span></span> <span data-ttu-id="6def4-234">该过程由`_broadcastLoop`中的计时器`Broadcaster`类。</span><span class="sxs-lookup"><span data-stu-id="6def4-234">The process is  managed by the `_broadcastLoop` timer from within the `Broadcaster` class.</span></span>

<span data-ttu-id="6def4-235">最后，而不是调用客户端方法从中心直接`Broadcaster`类需要获取对当前操作的引用`_hubContext`中心。</span><span class="sxs-lookup"><span data-stu-id="6def4-235">Lastly, instead of calling the client method from the hub directly, the `Broadcaster` class needs to get a reference to the currently operating `_hubContext` hub.</span></span> <span data-ttu-id="6def4-236">获取与引用`GlobalHost`。</span><span class="sxs-lookup"><span data-stu-id="6def4-236">It gets the reference with the `GlobalHost`.</span></span>

## <a name="add-smooth-animation"></a><span data-ttu-id="6def4-237">添加流畅的动画</span><span class="sxs-lookup"><span data-stu-id="6def4-237">Add smooth animation</span></span>

<span data-ttu-id="6def4-238">应用程序几乎已完成，但我们可以进行更多的一项改进。</span><span class="sxs-lookup"><span data-stu-id="6def4-238">The application is almost finished, but we could make one more improvement.</span></span> <span data-ttu-id="6def4-239">应用程序服务器的消息响应客户端上移动形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-239">The app moves the shape on the client in response to server messages.</span></span> <span data-ttu-id="6def4-240">而不是将该形状的位置设置为给定服务器的新位置，使用 JQuery UI 库`animate`函数。</span><span class="sxs-lookup"><span data-stu-id="6def4-240">Instead of setting the position of the shape to the new location given by the server, use the JQuery UI library's `animate` function.</span></span> <span data-ttu-id="6def4-241">它可以其当前和新位置之间顺利移动形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-241">It can move the shape smoothly between its current and new position.</span></span>

1. <span data-ttu-id="6def4-242">更新客户端`updateShape`中的方法*Default.html*文件看起来像突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="6def4-242">Update the client's `updateShape` method in the *Default.html* file to look like the highlighted code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. <span data-ttu-id="6def4-243">选择播放按钮启动应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-243">Select the play button to start the application.</span></span>

1. <span data-ttu-id="6def4-244">复制页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="6def4-244">Copy the page's URL.</span></span>

1. <span data-ttu-id="6def4-245">打开另一个浏览器并将 URL 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="6def4-245">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="6def4-246">将一个浏览器窗口中的形状。</span><span class="sxs-lookup"><span data-stu-id="6def4-246">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="6def4-247">在其他窗口中的形状的移动显示较低显得很不稳定。</span><span class="sxs-lookup"><span data-stu-id="6def4-247">The movement of the shape in the other window appears less jerky.</span></span> <span data-ttu-id="6def4-248">应用随时间而不是每个传入消息一次设置内插及其移动。</span><span class="sxs-lookup"><span data-stu-id="6def4-248">The app interpolates its movement over time rather than being set once per incoming message.</span></span>

<span data-ttu-id="6def4-249">此代码将从旧位置形状移到新。</span><span class="sxs-lookup"><span data-stu-id="6def4-249">This code moves the shape from the old location to the new one.</span></span> <span data-ttu-id="6def4-250">服务器可以提供动画时间间隔内的形状的位置。</span><span class="sxs-lookup"><span data-stu-id="6def4-250">The server gives the position of the shape over the course of the animation interval.</span></span> <span data-ttu-id="6def4-251">在这种情况下，这是 100 毫秒。</span><span class="sxs-lookup"><span data-stu-id="6def4-251">In this case, that's 100 milliseconds.</span></span> <span data-ttu-id="6def4-252">应用程序清除新动画开始之前，该形状上运行任何前一个动画。</span><span class="sxs-lookup"><span data-stu-id="6def4-252">The app clears any previous animation running on the shape before the new animation starts.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6def4-253">获取代码</span><span class="sxs-lookup"><span data-stu-id="6def4-253">Get the code</span></span>

[<span data-ttu-id="6def4-254">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="6def4-254">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

## <a name="additional-resources"></a><span data-ttu-id="6def4-255">其他资源</span><span class="sxs-lookup"><span data-stu-id="6def4-255">Additional resources</span></span>

<span data-ttu-id="6def4-256">你刚刚了解了有关的通信模式可用于开发联机游戏和其他模拟，如[ShootR 游戏使用 SignalR 创建](https://shootr.azurewebsites.net/)。</span><span class="sxs-lookup"><span data-stu-id="6def4-256">The communication paradigm you just learned about is useful for developing online games and other simulations, like [the ShootR game created with SignalR](https://shootr.azurewebsites.net/).</span></span>

<span data-ttu-id="6def4-257">有关 SignalR 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="6def4-257">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="6def4-258">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="6def4-258">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="6def4-259">SignalR GitHub 和示例</span><span class="sxs-lookup"><span data-stu-id="6def4-259">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="6def4-260">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="6def4-260">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="6def4-261">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6def4-261">Next steps</span></span>

<span data-ttu-id="6def4-262">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6def4-262">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6def4-263">设置项目</span><span class="sxs-lookup"><span data-stu-id="6def4-263">Set up the project</span></span>
> * <span data-ttu-id="6def4-264">创建基本应用程序</span><span class="sxs-lookup"><span data-stu-id="6def4-264">Created the base application</span></span>
> * <span data-ttu-id="6def4-265">应用程序启动时，映射到中心</span><span class="sxs-lookup"><span data-stu-id="6def4-265">Mapped to the hub when app starts</span></span>
> * <span data-ttu-id="6def4-266">添加客户端</span><span class="sxs-lookup"><span data-stu-id="6def4-266">Added the client</span></span>
> * <span data-ttu-id="6def4-267">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="6def4-267">Ran the app</span></span>
> * <span data-ttu-id="6def4-268">添加客户端循环</span><span class="sxs-lookup"><span data-stu-id="6def4-268">Added the client loop</span></span>
> * <span data-ttu-id="6def4-269">添加服务器循环</span><span class="sxs-lookup"><span data-stu-id="6def4-269">Added the server loop</span></span>
> * <span data-ttu-id="6def4-270">添加了流畅的动画</span><span class="sxs-lookup"><span data-stu-id="6def4-270">Added smooth animation</span></span>

<span data-ttu-id="6def4-271">转到下一步的文章，了解如何创建使用 ASP.NET SignalR 2 来提供服务器广播的功能的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="6def4-271">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="6def4-272">SignalR 2 和服务器广播</span><span class="sxs-lookup"><span data-stu-id="6def4-272">SignalR 2 and server broadcasting</span></span>](tutorial-server-broadcast-with-signalr.md)