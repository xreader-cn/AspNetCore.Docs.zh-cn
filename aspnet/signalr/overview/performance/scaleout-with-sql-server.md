---
uid: signalr/overview/performance/scaleout-with-sql-server
title: 使用 SQL Server 的 SignalR 横向扩展 |Microsoft Docs
author: bradygaster
description: Visual Studio 2013.NET 4.5 SignalR 本主题中使用软件版本信息有关早期版本的版本 2 的本主题的早期版本...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 98358b6e-9139-4239-ba3a-2d7dd74dd664
msc.legacyurl: /signalr/overview/performance/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 5984a5e6c3215e7dde8c09ef702bf6453730a3ee
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837892"
---
<a name="signalr-scaleout-with-sql-server"></a><span data-ttu-id="948e2-103">使用 SQL Server 的 SignalR 横向扩展</span><span class="sxs-lookup"><span data-stu-id="948e2-103">SignalR Scaleout with SQL Server</span></span>
====================
<span data-ttu-id="948e2-104">通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="948e2-104">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="948e2-105">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="948e2-105">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="948e2-106">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="948e2-106">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="948e2-107">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="948e2-107">.NET 4.5</span></span>
> - <span data-ttu-id="948e2-108">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="948e2-108">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="948e2-109">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="948e2-109">Previous versions of this topic</span></span>
>
> <span data-ttu-id="948e2-110">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="948e2-110">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="948e2-111">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="948e2-111">Questions and comments</span></span>
>
> <span data-ttu-id="948e2-112">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="948e2-112">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="948e2-113">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="948e2-113">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="948e2-114">在本教程中，将使用 SQL Server 将消息分布到两个单独的 IIS 实例中部署的 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="948e2-114">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="948e2-115">此外可以在单个测试计算机上，运行本教程中，但若要获取完整的效果，您需要 SignalR 应用程序部署到两个或多个服务器。</span><span class="sxs-lookup"><span data-stu-id="948e2-115">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="948e2-116">其中一台服务器，或单独的专用服务器上，还必须安装 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="948e2-116">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="948e2-117">另一种方法是运行本教程使用 Azure 上的 Vm。</span><span class="sxs-lookup"><span data-stu-id="948e2-117">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="948e2-118">系统必备</span><span class="sxs-lookup"><span data-stu-id="948e2-118">Prerequisites</span></span>

<span data-ttu-id="948e2-119">Microsoft SQL Server 2005 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="948e2-119">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="948e2-120">底板支持桌面和服务器版本的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="948e2-120">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="948e2-121">不支持 SQL Server Compact Edition 或 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="948e2-121">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="948e2-122">（如果你的应用程序托管在 Azure 上，考虑服务总线底板。）</span><span class="sxs-lookup"><span data-stu-id="948e2-122">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="948e2-123">概述</span><span class="sxs-lookup"><span data-stu-id="948e2-123">Overview</span></span>

<span data-ttu-id="948e2-124">我们进入详细教程之前，下面是将执行哪些操作的快速概述。</span><span class="sxs-lookup"><span data-stu-id="948e2-124">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="948e2-125">创建新的空数据库。</span><span class="sxs-lookup"><span data-stu-id="948e2-125">Create a new empty database.</span></span> <span data-ttu-id="948e2-126">此数据库中，基架将创建所需的表。</span><span class="sxs-lookup"><span data-stu-id="948e2-126">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="948e2-127">将以下 NuGet 包添加到你的应用程序：</span><span class="sxs-lookup"><span data-stu-id="948e2-127">Add these NuGet packages to your application:</span></span>

    - [<span data-ttu-id="948e2-128">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="948e2-128">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="948e2-129">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="948e2-129">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="948e2-130">创建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="948e2-130">Create a SignalR application.</span></span>
4. <span data-ttu-id="948e2-131">将以下代码添加到 Startup.cs 配置基架：</span><span class="sxs-lookup"><span data-stu-id="948e2-131">Add the following code to Startup.cs to configure the backplane:</span></span>

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

   <span data-ttu-id="948e2-132">此代码使用的默认值配置底板[TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx)并[MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx)。</span><span class="sxs-lookup"><span data-stu-id="948e2-132">This code configures the backplane with the default values for [TableCount](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.sqlscaleoutconfiguration.tablecount(v=vs.118).aspx) and [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx).</span></span> <span data-ttu-id="948e2-133">有关更改这些值的信息，请参阅[SignalR 性能：横向扩展指标](signalr-performance.md#scaleout_metrics)。</span><span class="sxs-lookup"><span data-stu-id="948e2-133">For information on changing these values, see [SignalR Performance: Scaleout Metrics](signalr-performance.md#scaleout_metrics).</span></span>

## <a name="configure-the-database"></a><span data-ttu-id="948e2-134">配置数据库</span><span class="sxs-lookup"><span data-stu-id="948e2-134">Configure the Database</span></span>

<span data-ttu-id="948e2-135">确定应用程序是否将使用 Windows 身份验证或 SQL Server 身份验证来访问数据库。</span><span class="sxs-lookup"><span data-stu-id="948e2-135">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="948e2-136">无论如何，请确保数据库用户有权登录，创建架构，并创建表。</span><span class="sxs-lookup"><span data-stu-id="948e2-136">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="948e2-137">创建基架以使用新的数据库。</span><span class="sxs-lookup"><span data-stu-id="948e2-137">Create a new database for the backplane to use.</span></span> <span data-ttu-id="948e2-138">可以为数据库提供任何名称。</span><span class="sxs-lookup"><span data-stu-id="948e2-138">You can give the database any name.</span></span> <span data-ttu-id="948e2-139">无需在数据库中; 中创建的任何表基架将创建所需的表。</span><span class="sxs-lookup"><span data-stu-id="948e2-139">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="948e2-140">启用 Service Broker</span><span class="sxs-lookup"><span data-stu-id="948e2-140">Enable Service Broker</span></span>

<span data-ttu-id="948e2-141">建议为底板数据库启用服务中介程序。</span><span class="sxs-lookup"><span data-stu-id="948e2-141">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="948e2-142">Service Broker 提供了对消息传送和队列在 SQL Server，可让更高效地接收更新底板中的本机支持。</span><span class="sxs-lookup"><span data-stu-id="948e2-142">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="948e2-143">（但是，基架也无需正常 Service Broker。）</span><span class="sxs-lookup"><span data-stu-id="948e2-143">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="948e2-144">若要检查是否启用 Service Broker，请查询**是\_broker\_启用**中的列**sys.databases**目录视图。</span><span class="sxs-lookup"><span data-stu-id="948e2-144">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="948e2-145">若要启用服务中介程序，请使用以下 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="948e2-145">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="948e2-146">如果此查询出现死锁，请确保没有应用程序连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="948e2-146">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>


<span data-ttu-id="948e2-147">如果已启用跟踪，跟踪还会显示是否启用 Service Broker。</span><span class="sxs-lookup"><span data-stu-id="948e2-147">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="948e2-148">创建 SignalR 应用程序</span><span class="sxs-lookup"><span data-stu-id="948e2-148">Create a SignalR Application</span></span>

<span data-ttu-id="948e2-149">创建 SignalR 应用程序按照以下教程之一：</span><span class="sxs-lookup"><span data-stu-id="948e2-149">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="948e2-150">SignalR 2.0 入门</span><span class="sxs-lookup"><span data-stu-id="948e2-150">Getting Started with SignalR 2.0</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="948e2-151">SignalR 2.0 和 MVC 5 入门</span><span class="sxs-lookup"><span data-stu-id="948e2-151">Getting Started with SignalR 2.0 and MVC 5</span></span>](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)

<span data-ttu-id="948e2-152">接下来，我们将修改要支持使用 SQL Server 横向扩展的聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="948e2-152">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="948e2-153">首先，将 SignalR.SqlServer NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="948e2-153">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="948e2-154">在 Visual Studio 中，从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="948e2-154">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="948e2-155">在包管理器控制台窗口中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="948e2-155">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="948e2-156">接下来，打开 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="948e2-156">Next, open the Startup.cs file.</span></span> <span data-ttu-id="948e2-157">将以下代码添加到**配置**方法：</span><span class="sxs-lookup"><span data-stu-id="948e2-157">Add the following code to the **Configure** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="948e2-158">部署和运行应用程序</span><span class="sxs-lookup"><span data-stu-id="948e2-158">Deploy and Run the Application</span></span>

<span data-ttu-id="948e2-159">准备 Windows Server 实例将 SignalR 应用程序部署。</span><span class="sxs-lookup"><span data-stu-id="948e2-159">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="948e2-160">添加 IIS 角色。</span><span class="sxs-lookup"><span data-stu-id="948e2-160">Add the IIS role.</span></span> <span data-ttu-id="948e2-161">包含"应用程序开发"功能，包括 WebSocket 协议。</span><span class="sxs-lookup"><span data-stu-id="948e2-161">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="948e2-162">此外包括管理服务 （在"管理工具"列出）。</span><span class="sxs-lookup"><span data-stu-id="948e2-162">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="948e2-163">**安装 Web 部署 3.0。**</span><span class="sxs-lookup"><span data-stu-id="948e2-163">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="948e2-164">当你运行 IIS 管理器，它将提示你安装 Microsoft Web 平台，或者可以[下载 intstaller](https://go.microsoft.com/fwlink/?LinkId=255386)。</span><span class="sxs-lookup"><span data-stu-id="948e2-164">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="948e2-165">在平台安装程序中，搜索 Web 部署并安装 Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="948e2-165">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="948e2-166">检查 Web 管理服务正在运行。</span><span class="sxs-lookup"><span data-stu-id="948e2-166">Check that the Web Management Service is running.</span></span> <span data-ttu-id="948e2-167">如果没有，请启动服务。</span><span class="sxs-lookup"><span data-stu-id="948e2-167">If not, start the service.</span></span> <span data-ttu-id="948e2-168">（如果在 Windows 服务列表中，看不到 Web 管理服务，请确保添加 IIS 角色时安装管理服务。）</span><span class="sxs-lookup"><span data-stu-id="948e2-168">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="948e2-169">最后，打开 TCP 端口 8172。</span><span class="sxs-lookup"><span data-stu-id="948e2-169">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="948e2-170">这是 Web 部署工具使用的端口。</span><span class="sxs-lookup"><span data-stu-id="948e2-170">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="948e2-171">现在已准备好部署到服务器从开发计算机的 Visual Studio 项目。</span><span class="sxs-lookup"><span data-stu-id="948e2-171">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="948e2-172">在解决方案资源管理器，右键单击解决方案，然后单击**发布**。</span><span class="sxs-lookup"><span data-stu-id="948e2-172">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="948e2-173">有关更详细的有关 web 部署的文档，请参阅[用于 Visual Studio 和 ASP.NET 的 Web 部署内容映射](../../../whitepapers/aspnet-web-deployment-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="948e2-173">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="948e2-174">如果部署到两个服务器应用程序，可以在一个单独的浏览器窗口中打开每个实例，并查看它们每个接收来自其他 SignalR 消息。</span><span class="sxs-lookup"><span data-stu-id="948e2-174">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="948e2-175">（当然，在生产环境中，两个服务器将位于负载均衡器后面。）</span><span class="sxs-lookup"><span data-stu-id="948e2-175">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="948e2-176">运行应用程序后，可以看到 SignalR 自动具有在数据库中创建表：</span><span class="sxs-lookup"><span data-stu-id="948e2-176">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="948e2-177">SignalR 管理表。</span><span class="sxs-lookup"><span data-stu-id="948e2-177">SignalR manages the tables.</span></span> <span data-ttu-id="948e2-178">只要部署应用程序，不删除行、 修改表，等等。</span><span class="sxs-lookup"><span data-stu-id="948e2-178">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>
