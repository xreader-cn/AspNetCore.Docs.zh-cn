---
uid: signalr/overview/older-versions/scaleout-with-sql-server
title: 使用 SQL Server 的 SignalR 横向扩展 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 1dca7967-8296-444a-9533-837eb284e78c
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-sql-server
msc.type: authoredcontent
ms.openlocfilehash: 7fd05a4487be4cc96fa945cf08226841e3f01806
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835930"
---
<a name="signalr-scaleout-with-sql-server-signalr-1x"></a><span data-ttu-id="093c9-102">使用 SQL Server 的 SignalR 横向扩展 (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="093c9-102">SignalR Scaleout with SQL Server (SignalR 1.x)</span></span>
====================
<span data-ttu-id="093c9-103">通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="093c9-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="093c9-104">在本教程中，将使用 SQL Server 将消息分布到两个单独的 IIS 实例中部署的 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="093c9-104">In this tutorial, you will use SQL Server to distribute messages across a SignalR application that is deployed in two separate IIS instances.</span></span> <span data-ttu-id="093c9-105">此外可以在单个测试计算机上，运行本教程中，但若要获取完整的效果，您需要 SignalR 应用程序部署到两个或多个服务器。</span><span class="sxs-lookup"><span data-stu-id="093c9-105">You can also run this tutorial on a single test machine, but to get the full effect, you need to deploy the SignalR application to two or more servers.</span></span> <span data-ttu-id="093c9-106">其中一台服务器，或单独的专用服务器上，还必须安装 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="093c9-106">You must also install SQL Server on one of the servers, or on a separate dedicated server.</span></span> <span data-ttu-id="093c9-107">另一种方法是运行本教程使用 Azure 上的 Vm。</span><span class="sxs-lookup"><span data-stu-id="093c9-107">Another option is to run the tutorial using VMs on Azure.</span></span>

![](scaleout-with-sql-server/_static/image1.png)

## <a name="prerequisites"></a><span data-ttu-id="093c9-108">系统必备</span><span class="sxs-lookup"><span data-stu-id="093c9-108">Prerequisites</span></span>

<span data-ttu-id="093c9-109">Microsoft SQL Server 2005 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="093c9-109">Microsoft SQL Server 2005 or later.</span></span> <span data-ttu-id="093c9-110">底板支持桌面和服务器版本的 SQL Server。</span><span class="sxs-lookup"><span data-stu-id="093c9-110">The backplane supports both desktop and server editions of SQL Server.</span></span> <span data-ttu-id="093c9-111">不支持 SQL Server Compact Edition 或 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="093c9-111">It does not support SQL Server Compact Edition or Azure SQL Database.</span></span> <span data-ttu-id="093c9-112">（如果你的应用程序托管在 Azure 上，考虑服务总线底板。）</span><span class="sxs-lookup"><span data-stu-id="093c9-112">(If your application is hosted on Azure, consider the Service Bus backplane instead.)</span></span>

## <a name="overview"></a><span data-ttu-id="093c9-113">概述</span><span class="sxs-lookup"><span data-stu-id="093c9-113">Overview</span></span>

<span data-ttu-id="093c9-114">我们进入详细教程之前，下面是将执行哪些操作的快速概述。</span><span class="sxs-lookup"><span data-stu-id="093c9-114">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="093c9-115">创建新的空数据库。</span><span class="sxs-lookup"><span data-stu-id="093c9-115">Create a new empty database.</span></span> <span data-ttu-id="093c9-116">此数据库中，基架将创建所需的表。</span><span class="sxs-lookup"><span data-stu-id="093c9-116">The backplane will create the necessary tables in this database.</span></span>
2. <span data-ttu-id="093c9-117">将以下 NuGet 包添加到你的应用程序：</span><span class="sxs-lookup"><span data-stu-id="093c9-117">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="093c9-118">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="093c9-118">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="093c9-119">Microsoft.AspNet.SignalR.SqlServer</span><span class="sxs-lookup"><span data-stu-id="093c9-119">Microsoft.AspNet.SignalR.SqlServer</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.SqlServer)
3. <span data-ttu-id="093c9-120">创建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="093c9-120">Create a SignalR application.</span></span>
4. <span data-ttu-id="093c9-121">将以下代码添加到 Global.asax 配置基架：</span><span class="sxs-lookup"><span data-stu-id="093c9-121">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-sql-server/samples/sample1.cs)]

## <a name="configure-the-database"></a><span data-ttu-id="093c9-122">配置数据库</span><span class="sxs-lookup"><span data-stu-id="093c9-122">Configure the Database</span></span>

<span data-ttu-id="093c9-123">确定应用程序是否将使用 Windows 身份验证或 SQL Server 身份验证来访问数据库。</span><span class="sxs-lookup"><span data-stu-id="093c9-123">Decide whether the application will use Windows authentication or SQL Server authentication to access the database.</span></span> <span data-ttu-id="093c9-124">无论如何，请确保数据库用户有权登录，创建架构，并创建表。</span><span class="sxs-lookup"><span data-stu-id="093c9-124">Regardless, make sure the database user has permissions to log in, create schemas, and create tables.</span></span>

<span data-ttu-id="093c9-125">创建基架以使用新的数据库。</span><span class="sxs-lookup"><span data-stu-id="093c9-125">Create a new database for the backplane to use.</span></span> <span data-ttu-id="093c9-126">可以为数据库提供任何名称。</span><span class="sxs-lookup"><span data-stu-id="093c9-126">You can give the database any name.</span></span> <span data-ttu-id="093c9-127">无需在数据库中; 中创建的任何表基架将创建所需的表。</span><span class="sxs-lookup"><span data-stu-id="093c9-127">You don't need to create any tables in the database; the backplane will create the necessary tables.</span></span>

![](scaleout-with-sql-server/_static/image2.png)

## <a name="enable-service-broker"></a><span data-ttu-id="093c9-128">启用 Service Broker</span><span class="sxs-lookup"><span data-stu-id="093c9-128">Enable Service Broker</span></span>

<span data-ttu-id="093c9-129">建议为底板数据库启用服务中介程序。</span><span class="sxs-lookup"><span data-stu-id="093c9-129">It is recommended to enable Service Broker for the backplane database.</span></span> <span data-ttu-id="093c9-130">Service Broker 提供了对消息传送和队列在 SQL Server，可让更高效地接收更新底板中的本机支持。</span><span class="sxs-lookup"><span data-stu-id="093c9-130">Service Broker provides native support for messaging and queuing in SQL Server, which lets the backplane receive updates more efficiently.</span></span> <span data-ttu-id="093c9-131">（但是，基架也无需正常 Service Broker。）</span><span class="sxs-lookup"><span data-stu-id="093c9-131">(However, the backplane also works without Service Broker.)</span></span>

<span data-ttu-id="093c9-132">若要检查是否启用 Service Broker，请查询**是\_broker\_启用**中的列**sys.databases**目录视图。</span><span class="sxs-lookup"><span data-stu-id="093c9-132">To check whether Service Broker is enabled, query the **is\_broker\_enabled** column in the **sys.databases** catalog view.</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample2.sql)]

![](scaleout-with-sql-server/_static/image3.png)

<span data-ttu-id="093c9-133">若要启用服务中介程序，请使用以下 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="093c9-133">To enable Service Broker, use the following SQL query:</span></span>

[!code-sql[Main](scaleout-with-sql-server/samples/sample3.sql)]

> [!NOTE]
> <span data-ttu-id="093c9-134">如果此查询出现死锁，请确保没有应用程序连接到数据库。</span><span class="sxs-lookup"><span data-stu-id="093c9-134">If this query appears to deadlock, make sure there are no applications connected to the DB.</span></span>

<span data-ttu-id="093c9-135">如果已启用跟踪，跟踪还会显示是否启用 Service Broker。</span><span class="sxs-lookup"><span data-stu-id="093c9-135">If you have enabled tracing, the traces will also show whether Service Broker is enabled.</span></span>

## <a name="create-a-signalr-application"></a><span data-ttu-id="093c9-136">创建 SignalR 应用程序</span><span class="sxs-lookup"><span data-stu-id="093c9-136">Create a SignalR Application</span></span>

<span data-ttu-id="093c9-137">创建 SignalR 应用程序按照以下教程之一：</span><span class="sxs-lookup"><span data-stu-id="093c9-137">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="093c9-138">SignalR 入门</span><span class="sxs-lookup"><span data-stu-id="093c9-138">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="093c9-139">使用 SignalR 和 MVC 4 入门</span><span class="sxs-lookup"><span data-stu-id="093c9-139">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="093c9-140">接下来，我们将修改要支持使用 SQL Server 横向扩展的聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="093c9-140">Next, we'll modify the chat application to support scaleout with SQL Server.</span></span> <span data-ttu-id="093c9-141">首先，将 SignalR.SqlServer NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="093c9-141">First, add the SignalR.SqlServer NuGet package to your project.</span></span> <span data-ttu-id="093c9-142">在 Visual Studio 中，从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="093c9-142">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="093c9-143">在包管理器控制台窗口中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="093c9-143">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-sql-server/samples/sample4.ps1)]

<span data-ttu-id="093c9-144">接下来，打开 Global.asax 文件。</span><span class="sxs-lookup"><span data-stu-id="093c9-144">Next, open the Global.asax file.</span></span> <span data-ttu-id="093c9-145">将以下代码添加到**应用程序\_启动**方法：</span><span class="sxs-lookup"><span data-stu-id="093c9-145">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-sql-server/samples/sample5.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="093c9-146">部署和运行应用程序</span><span class="sxs-lookup"><span data-stu-id="093c9-146">Deploy and Run the Application</span></span>

<span data-ttu-id="093c9-147">准备 Windows Server 实例将 SignalR 应用程序部署。</span><span class="sxs-lookup"><span data-stu-id="093c9-147">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="093c9-148">添加 IIS 角色。</span><span class="sxs-lookup"><span data-stu-id="093c9-148">Add the IIS role.</span></span> <span data-ttu-id="093c9-149">包含"应用程序开发"功能，包括 WebSocket 协议。</span><span class="sxs-lookup"><span data-stu-id="093c9-149">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-sql-server/_static/image4.png)

<span data-ttu-id="093c9-150">此外包括管理服务 （在"管理工具"列出）。</span><span class="sxs-lookup"><span data-stu-id="093c9-150">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-sql-server/_static/image5.png)

<span data-ttu-id="093c9-151">**安装 Web 部署 3.0。**</span><span class="sxs-lookup"><span data-stu-id="093c9-151">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="093c9-152">当你运行 IIS 管理器，它将提示你安装 Microsoft Web 平台，或者可以[下载 intstaller](https://go.microsoft.com/fwlink/?LinkId=255386)。</span><span class="sxs-lookup"><span data-stu-id="093c9-152">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="093c9-153">在平台安装程序中，搜索 Web 部署并安装 Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="093c9-153">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-sql-server/_static/image6.png)

<span data-ttu-id="093c9-154">检查 Web 管理服务正在运行。</span><span class="sxs-lookup"><span data-stu-id="093c9-154">Check that the Web Management Service is running.</span></span> <span data-ttu-id="093c9-155">如果没有，请启动服务。</span><span class="sxs-lookup"><span data-stu-id="093c9-155">If not, start the service.</span></span> <span data-ttu-id="093c9-156">（如果在 Windows 服务列表中，看不到 Web 管理服务，请确保添加 IIS 角色时安装管理服务。）</span><span class="sxs-lookup"><span data-stu-id="093c9-156">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="093c9-157">最后，打开 TCP 端口 8172。</span><span class="sxs-lookup"><span data-stu-id="093c9-157">Finally, open port 8172 for TCP.</span></span> <span data-ttu-id="093c9-158">这是 Web 部署工具使用的端口。</span><span class="sxs-lookup"><span data-stu-id="093c9-158">This is the port that the Web Deploy tool uses.</span></span>

<span data-ttu-id="093c9-159">现在已准备好部署到服务器从开发计算机的 Visual Studio 项目。</span><span class="sxs-lookup"><span data-stu-id="093c9-159">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="093c9-160">在解决方案资源管理器，右键单击解决方案，然后单击**发布**。</span><span class="sxs-lookup"><span data-stu-id="093c9-160">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="093c9-161">有关更详细的有关 web 部署的文档，请参阅[用于 Visual Studio 和 ASP.NET 的 Web 部署内容映射](../../../whitepapers/aspnet-web-deployment-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="093c9-161">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="093c9-162">如果部署到两个服务器应用程序，可以在一个单独的浏览器窗口中打开每个实例，并查看它们每个接收来自其他 SignalR 消息。</span><span class="sxs-lookup"><span data-stu-id="093c9-162">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="093c9-163">（当然，在生产环境中，两个服务器将位于负载均衡器后面。）</span><span class="sxs-lookup"><span data-stu-id="093c9-163">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-sql-server/_static/image7.png)

<span data-ttu-id="093c9-164">运行应用程序后，可以看到 SignalR 自动具有在数据库中创建表：</span><span class="sxs-lookup"><span data-stu-id="093c9-164">After you run the application, you can see that SignalR has automatically created tables in the database:</span></span>

![](scaleout-with-sql-server/_static/image8.png)

<span data-ttu-id="093c9-165">SignalR 管理表。</span><span class="sxs-lookup"><span data-stu-id="093c9-165">SignalR manages the tables.</span></span> <span data-ttu-id="093c9-166">只要部署应用程序，不删除行、 修改表，等等。</span><span class="sxs-lookup"><span data-stu-id="093c9-166">As long as your application is deployed, don't delete rows, modify the table, and so forth.</span></span>
