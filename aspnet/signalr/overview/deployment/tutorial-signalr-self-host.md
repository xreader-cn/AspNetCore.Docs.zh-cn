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
<a name="tutorial-signalr-self-host"></a><span data-ttu-id="4c43a-104">教程：自承载 SignalR</span><span class="sxs-lookup"><span data-stu-id="4c43a-104">Tutorial: SignalR Self-Host</span></span>
====================
<span data-ttu-id="4c43a-105">通过[Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="4c43a-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

[<span data-ttu-id="4c43a-106">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="4c43a-106">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-Self-Host-Sample-6da0f383)

> <span data-ttu-id="4c43a-107">本教程演示如何创建自承载的 SignalR 2 服务器，以及如何使用 JavaScript 客户端连接到它。</span><span class="sxs-lookup"><span data-stu-id="4c43a-107">This tutorial shows how to create a self-hosted SignalR 2 server, and how to connect to it with a JavaScript client.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="4c43a-108">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="4c43a-108">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="4c43a-109">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="4c43a-109">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="4c43a-110">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="4c43a-110">.NET 4.5</span></span>
> - <span data-ttu-id="4c43a-111">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="4c43a-111">SignalR version 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="4c43a-112">本教程使用 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="4c43a-112">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="4c43a-113">若要学习本教程使用 Visual Studio 2012，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="4c43a-113">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="4c43a-114">更新你[程序包管理器](http://docs.nuget.org/docs/start-here/installing-nuget)到最新版本。</span><span class="sxs-lookup"><span data-stu-id="4c43a-114">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="4c43a-115">安装[Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)。</span><span class="sxs-lookup"><span data-stu-id="4c43a-115">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="4c43a-116">在 Web 平台安装程序中，搜索并安装**ASP.NET 和 Web 工具 2013.1 适用于 Visual Studio 2012**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-116">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="4c43a-117">这将安装 Visual Studio 模板的 SignalR 类，如**中心**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-117">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="4c43a-118">某些模板 (如**OWIN 启动类**) 将不可用; 对于这些数据，改为使用的类文件。</span><span class="sxs-lookup"><span data-stu-id="4c43a-118">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="4c43a-119">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="4c43a-119">Questions and comments</span></span>
>
> <span data-ttu-id="4c43a-120">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="4c43a-120">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="4c43a-121">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="4c43a-121">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


## <a name="overview"></a><span data-ttu-id="4c43a-122">概述</span><span class="sxs-lookup"><span data-stu-id="4c43a-122">Overview</span></span>

<span data-ttu-id="4c43a-123">SignalR 服务器通常托管在 ASP.NET 应用程序在 IIS 中，但它也可以是自承载 （如控制台应用程序或 Windows 服务） 使用的自承载的库。</span><span class="sxs-lookup"><span data-stu-id="4c43a-123">A SignalR server is usually hosted in an ASP.NET application in IIS, but it can also be self-hosted (such as in a console application or Windows service) using the self-host library.</span></span> <span data-ttu-id="4c43a-124">此库，如所有 SignalR 2 基于 OWIN ([Open Web Interface for.NET](http://owin.org))。</span><span class="sxs-lookup"><span data-stu-id="4c43a-124">This library, like all of SignalR 2, is built on OWIN ([Open Web Interface for .NET](http://owin.org)).</span></span> <span data-ttu-id="4c43a-125">OWIN 定义.NET web 服务器和 web 应用程序之间的抽象。</span><span class="sxs-lookup"><span data-stu-id="4c43a-125">OWIN defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="4c43a-126">OWIN 将从服务器中，这使 OWIN 适合于自承载在 IIS 外部自己进程中的 web 应用程序的 web 应用程序中分离出来。</span><span class="sxs-lookup"><span data-stu-id="4c43a-126">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>

<span data-ttu-id="4c43a-127">不在 IIS 中承载的原因包括：</span><span class="sxs-lookup"><span data-stu-id="4c43a-127">Reasons for not hosting in IIS include:</span></span>

- <span data-ttu-id="4c43a-128">IIS 不可用或有意义，如不含 IIS 的现有服务器场的位置的环境。</span><span class="sxs-lookup"><span data-stu-id="4c43a-128">Environments where IIS is not available or desirable, such as an existing server farm without IIS.</span></span>
- <span data-ttu-id="4c43a-129">需要避免使用 IIS 的性能开销。</span><span class="sxs-lookup"><span data-stu-id="4c43a-129">The performance overhead of IIS needs to be avoided.</span></span>
- <span data-ttu-id="4c43a-130">SignalR 功能是添加到 Windows 服务、 Azure 辅助角色或其他进程中运行的现有应用程序。</span><span class="sxs-lookup"><span data-stu-id="4c43a-130">SignalR functionality is to be added to an exising application that runs in a Windows Service, Azure worker role, or other process.</span></span>

<span data-ttu-id="4c43a-131">如果正在开发一个解决方案作为自托管出于性能原因，建议测试也在 IIS 中托管的应用程序以确定的性能优势。</span><span class="sxs-lookup"><span data-stu-id="4c43a-131">If a solution is being developed as self-host for performance reasons, it's recommended to also test the application hosted in IIS to determine the performance benefit.</span></span>

<span data-ttu-id="4c43a-132">本教程包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="4c43a-132">This tutorial contains the following sections:</span></span>

- [<span data-ttu-id="4c43a-133">创建服务器</span><span class="sxs-lookup"><span data-stu-id="4c43a-133">Creating the server</span></span>](#server)
- [<span data-ttu-id="4c43a-134">访问与 JavaScript 客户端服务器</span><span class="sxs-lookup"><span data-stu-id="4c43a-134">Accessing the server with a JavaScript client</span></span>](#js)

<a id="server"></a>

## <a name="creating-the-server"></a><span data-ttu-id="4c43a-135">创建服务器</span><span class="sxs-lookup"><span data-stu-id="4c43a-135">Creating the server</span></span>

<span data-ttu-id="4c43a-136">在本教程中，你将在控制台应用程序中，创建托管的服务器，但服务器可以托管在任何类型的过程中，如 Windows 服务或 Azure 辅助角色。</span><span class="sxs-lookup"><span data-stu-id="4c43a-136">In this tutorial, you'll create a server that's hosted in a console application, but the server can be hosted in any sort of process, such as a Windows service or Azure worker role.</span></span> <span data-ttu-id="4c43a-137">有关示例代码用于托管 Windows 服务中的 SignalR 服务器，请参阅[Self-Hosting Windows 服务中的 SignalR](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3)。</span><span class="sxs-lookup"><span data-stu-id="4c43a-137">For sample code for hosting a SignalR server in a Windows Service, see [Self-Hosting SignalR in a Windows Service](https://code.msdn.microsoft.com/SignalR-self-hosted-in-6ff7e6c3).</span></span>

1. <span data-ttu-id="4c43a-138">使用管理员特权打开 Visual Studio 2013。</span><span class="sxs-lookup"><span data-stu-id="4c43a-138">Open Visual Studio 2013 with administrator privileges.</span></span> <span data-ttu-id="4c43a-139">选择**文件**，**新项目**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-139">Select **File**, **New Project**.</span></span> <span data-ttu-id="4c43a-140">选择**Windows**下**Visual C#** 中的节点**模板**窗格，然后选择**控制台应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="4c43a-140">Select **Windows** under the **Visual C#** node in the **Templates** pane, and select the **Console Application** template.</span></span> <span data-ttu-id="4c43a-141">命名新项目"SignalRSelfHost"，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-141">Name the new project "SignalRSelfHost" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image1.png)
2. <span data-ttu-id="4c43a-142">通过选择打开 NuGet 包管理器控制台**工具** > **NuGet 包管理器** > **程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-142">Open the NuGet package manager console by selecting **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>
3. <span data-ttu-id="4c43a-143">在包管理器控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4c43a-143">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample1.ps1)]

    <span data-ttu-id="4c43a-144">此命令将 SignalR 2 自托管库添加到项目。</span><span class="sxs-lookup"><span data-stu-id="4c43a-144">This command adds the SignalR 2 Self-Host libraries to the project.</span></span>
4. <span data-ttu-id="4c43a-145">在包管理器控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="4c43a-145">In the package manager console, enter the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample2.ps1)]

    <span data-ttu-id="4c43a-146">此命令将 Microsoft.Owin.Cors 库添加到项目。</span><span class="sxs-lookup"><span data-stu-id="4c43a-146">This command adds the Microsoft.Owin.Cors library to the project.</span></span> <span data-ttu-id="4c43a-147">跨域支持，这是必需的应用程序的托管 SignalR 和不同的域中的网页客户端将用于此库。</span><span class="sxs-lookup"><span data-stu-id="4c43a-147">This library will be used for cross-domain support, which is required for applications that host SignalR and a web page client in different domains.</span></span> <span data-ttu-id="4c43a-148">由于你将承载 SignalR 服务器和不同的端口上的 web 客户端，这意味着必须为这些组件之间的通信启用跨域。</span><span class="sxs-lookup"><span data-stu-id="4c43a-148">Since you'll be hosting the SignalR server and the web client on different ports, this means that cross-domain must be enabled for communication between these components.</span></span>
5. <span data-ttu-id="4c43a-149">将 Program.cs 的内容替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="4c43a-149">Replace the contents of Program.cs with the following code.</span></span>

    [!code-csharp[Main](tutorial-signalr-self-host/samples/sample3.cs)]

    <span data-ttu-id="4c43a-150">上面的代码包括三个类：</span><span class="sxs-lookup"><span data-stu-id="4c43a-150">The above code includes three classes:</span></span>

    - <span data-ttu-id="4c43a-151">**程序**，其中包括**Main**定义执行主路径的方法。</span><span class="sxs-lookup"><span data-stu-id="4c43a-151">**Program**, including the **Main** method defining the primary path of execution.</span></span> <span data-ttu-id="4c43a-152">在此方法中，类型的 web 应用程序**启动**开始时间为指定的 URL (`http://localhost:8080`)。</span><span class="sxs-lookup"><span data-stu-id="4c43a-152">In this method, a web application of type **Startup** is started at the specified URL (`http://localhost:8080`).</span></span> <span data-ttu-id="4c43a-153">如果安全需要的终结点上，可以实现 SSL。</span><span class="sxs-lookup"><span data-stu-id="4c43a-153">If security is required on the endpoint, SSL can be implemented.</span></span> <span data-ttu-id="4c43a-154">请参阅[如何：使用 SSL 证书配置端口](https://msdn.microsoft.com/library/ms733791.aspx)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="4c43a-154">See [How to: Configure a Port with an SSL Certificate](https://msdn.microsoft.com/library/ms733791.aspx) for more information.</span></span>
    - <span data-ttu-id="4c43a-155">**启动**、 包含 SignalR 服务器的配置的类 (本教程使用的唯一配置是在调用`UseCors`)，以及对调用`MapSignalR`，用于在项目中创建路由的中心的任何对象。</span><span class="sxs-lookup"><span data-stu-id="4c43a-155">**Startup**, the class containing the configuration for the SignalR server (the only configuration this tutorial uses is the call to `UseCors`), and the call to `MapSignalR`, which creates routes for any Hub objects in the project.</span></span>
    - <span data-ttu-id="4c43a-156">**MyHub**，应用程序将向客户端提供的 SignalR Hub 类。</span><span class="sxs-lookup"><span data-stu-id="4c43a-156">**MyHub**, the SignalR Hub class that the application will provide to clients.</span></span> <span data-ttu-id="4c43a-157">此类有一个单一的方法，**发送**，客户端将调用以将一条消息广播到所有其他连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="4c43a-157">This class has a single method, **Send**, that clients will call to broadcast a message to all other connected clients.</span></span>
6. <span data-ttu-id="4c43a-158">编译并运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="4c43a-158">Compile and run the application.</span></span> <span data-ttu-id="4c43a-159">服务器正在运行的地址应显示在控制台窗口中。</span><span class="sxs-lookup"><span data-stu-id="4c43a-159">The address that the server is running should show in a console window.</span></span>

    ![](tutorial-signalr-self-host/_static/image2.png)
7. <span data-ttu-id="4c43a-160">如果执行失败，出现异常`System.Reflection.TargetInvocationException was unhandled`，将需要使用管理员权限重新启动 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="4c43a-160">If execution fails with the exception `System.Reflection.TargetInvocationException was unhandled`, you will need to restart Visual Studio with administrator privileges.</span></span>
8. <span data-ttu-id="4c43a-161">停止应用程序，然后再继续执行下一节。</span><span class="sxs-lookup"><span data-stu-id="4c43a-161">Stop the application before proceeding to the next section.</span></span>

<a id="js"></a>

## <a name="accessing-the-server-with-a-javascript-client"></a><span data-ttu-id="4c43a-162">访问与 JavaScript 客户端服务器</span><span class="sxs-lookup"><span data-stu-id="4c43a-162">Accessing the server with a JavaScript client</span></span>

<span data-ttu-id="4c43a-163">在本部分中，您将使用从相同的 JavaScript 客户端[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)。</span><span class="sxs-lookup"><span data-stu-id="4c43a-163">In this section, you'll use the same JavaScript client from the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md).</span></span> <span data-ttu-id="4c43a-164">我们将仅向客户端，则需要显式定义中心 URL 的一处修改。</span><span class="sxs-lookup"><span data-stu-id="4c43a-164">We'll only make one modification to the client, which is to explicitly define the hub URL.</span></span> <span data-ttu-id="4c43a-165">与自承载的应用程序，服务器不一定是在相同的地址作为连接的 URL （由于反向代理和负载均衡器），因此需要显式定义该 URL。</span><span class="sxs-lookup"><span data-stu-id="4c43a-165">With a self-hosted application, the server may not necessarily be at the same address as the connection URL (due to reverse proxies and load balancers), so the URL needs to be defined explicitly.</span></span>

1. <span data-ttu-id="4c43a-166">在中**解决方案资源管理器**，右键单击解决方案并选择**添加**，**新项目**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-166">In **Solution Explorer**, right-click on the solution and select **Add**, **New Project**.</span></span> <span data-ttu-id="4c43a-167">选择**Web**节点，然后选择**ASP.NET Web 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="4c43a-167">Select the **Web** node, and select the **ASP.NET Web Application** template.</span></span> <span data-ttu-id="4c43a-168">命名项目"JavascriptClient"，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-168">Name the project "JavascriptClient" and click **OK**.</span></span>

    ![](tutorial-signalr-self-host/_static/image3.png)
2. <span data-ttu-id="4c43a-169">选择**空**模板，然后保留剩余的选项未选中。</span><span class="sxs-lookup"><span data-stu-id="4c43a-169">Select the **Empty** template, and leave the remaining options unselected.</span></span> <span data-ttu-id="4c43a-170">选择**创建项目**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-170">Select **Create Project**.</span></span>

    ![](tutorial-signalr-self-host/_static/image4.png)
3. <span data-ttu-id="4c43a-171">在包管理器控制台中，选择"JavascriptClient"项目中的**默认项目**下拉列表中，并执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4c43a-171">In the package manager console, select the "JavascriptClient" project in the **Default project** drop-down, and execute the following command:</span></span>

    [!code-powershell[Main](tutorial-signalr-self-host/samples/sample4.ps1)]

    <span data-ttu-id="4c43a-172">此命令将安装在客户端中将需要的 SignalR 和 JQuery 库。</span><span class="sxs-lookup"><span data-stu-id="4c43a-172">This command installs the SignalR and JQuery libraries that you'll need in the client.</span></span>
4. <span data-ttu-id="4c43a-173">右键单击项目并选择**外**，**新项**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-173">Right-click on your project and select **Add**, **New Item**.</span></span> <span data-ttu-id="4c43a-174">选择**Web**节点，然后选择 HTML 页。</span><span class="sxs-lookup"><span data-stu-id="4c43a-174">Select the **Web** node, and select HTML Page.</span></span> <span data-ttu-id="4c43a-175">将该页命名为**Default.html**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-175">Name the page **Default.html**.</span></span>

    ![](tutorial-signalr-self-host/_static/image5.png)
5. <span data-ttu-id="4c43a-176">将以下代码替换为新的 HTML 页面的内容。</span><span class="sxs-lookup"><span data-stu-id="4c43a-176">Replace the contents of the new HTML page with the following code.</span></span> <span data-ttu-id="4c43a-177">验证在此处的脚本引用匹配项目的 Scripts 文件夹中的脚本。</span><span class="sxs-lookup"><span data-stu-id="4c43a-177">Verify that the script references here match the scripts in the Scripts folder of the project.</span></span>

    [!code-html[Main](tutorial-signalr-self-host/samples/sample5.html?highlight=31-32)]

    <span data-ttu-id="4c43a-178">下面的代码 （在上面的代码示例中突出显示） 是对 （除了为 SignalR 版本 2 beta 版本升级代码） 获取开始本教程中使用的客户端所做的添加。</span><span class="sxs-lookup"><span data-stu-id="4c43a-178">The following code (highlighted in the code sample above) is the addition that you've made to the client used in the Getting Stared tutorial (in addition to upgrading the code to SignalR version 2 beta).</span></span> <span data-ttu-id="4c43a-179">这行代码显式设置的基本连接 URL SignalR 在服务器上。</span><span class="sxs-lookup"><span data-stu-id="4c43a-179">This line of code explicitly sets the base connection URL for SignalR on the server.</span></span>

    [!code-javascript[Main](tutorial-signalr-self-host/samples/sample6.js)]
6. <span data-ttu-id="4c43a-180">右键单击解决方案，然后选择**设置启动项目...**.选择**多个启动项目**单选按钮，并设置这两个项目的**操作**到**启动**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-180">Right-click on the solution, and select **Set Startup Projects...**. Select the **Multiple startup projects** radio button, and set both projects' **Action** to **Start**.</span></span>

    ![](tutorial-signalr-self-host/_static/image6.png)
7. <span data-ttu-id="4c43a-181">右键单击"Default.html"，然后选择**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="4c43a-181">Right-click on "Default.html" and select **Set As Start Page**.</span></span>
8. <span data-ttu-id="4c43a-182">运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="4c43a-182">Run the application.</span></span> <span data-ttu-id="4c43a-183">服务器和页上将启动。</span><span class="sxs-lookup"><span data-stu-id="4c43a-183">The server and page will launch.</span></span> <span data-ttu-id="4c43a-184">可能需要重新加载该网页 (或选择**继续**在调试器中) 如果页面可加载之前启动服务器。</span><span class="sxs-lookup"><span data-stu-id="4c43a-184">You may need to reload the web page (or select **Continue** in the debugger) if the page loads before the server is started.</span></span>
9. <span data-ttu-id="4c43a-185">在浏览器提供用出现提示时的用户名。</span><span class="sxs-lookup"><span data-stu-id="4c43a-185">In the browser, provide a username when prompted.</span></span> <span data-ttu-id="4c43a-186">将页面的 URL 复制到另一个浏览器选项卡或窗口，并提供其他用户名。</span><span class="sxs-lookup"><span data-stu-id="4c43a-186">Copy the page's URL into another browser tab or window and provide a different username.</span></span> <span data-ttu-id="4c43a-187">你将能够将消息从一个浏览器窗格中发送到另一个，如入门教程中所示。</span><span class="sxs-lookup"><span data-stu-id="4c43a-187">You will be able to send messages from one browser pane to the other, as in the Getting Started tutorial.</span></span>
