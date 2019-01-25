---
uid: signalr/overview/older-versions/scaleout-with-redis
title: 使用 Redis 的 SignalR 横向扩展 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: ''
ms.author: bradyg
ms.date: 05/01/2013
ms.assetid: 6abecf80-8ffa-41ba-b0d9-1d9edbe7687b
msc.legacyurl: /signalr/overview/older-versions/scaleout-with-redis
msc.type: authoredcontent
ms.openlocfilehash: 74294bd04d5649f2ec54e58adb744f5e30525162
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836410"
---
<a name="signalr-scaleout-with-redis-signalr-1x"></a><span data-ttu-id="66beb-102">使用 Redis 的 SignalR 横向扩展 (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="66beb-102">SignalR Scaleout with Redis (SignalR 1.x)</span></span>
====================
<span data-ttu-id="66beb-103">通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="66beb-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="66beb-104">在本教程中，您将使用[Redis](http://redis.io/)若要部署在两个单独的 IIS 实例的 SignalR 应用程序间分发消息。</span><span class="sxs-lookup"><span data-stu-id="66beb-104">In this tutorial, you will use [Redis](http://redis.io/) to distribute messages across a SignalR application that is deployed on two separate IIS instances.</span></span>

<span data-ttu-id="66beb-105">Redis 是内存中键 / 值存储。</span><span class="sxs-lookup"><span data-stu-id="66beb-105">Redis is an in-memory key-value store.</span></span> <span data-ttu-id="66beb-106">它还支持发布/订阅模型的消息传送系统。</span><span class="sxs-lookup"><span data-stu-id="66beb-106">It also supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="66beb-107">Redis 的 SignalR 基架使用发布/订阅功能以将消息转发到其他服务器。</span><span class="sxs-lookup"><span data-stu-id="66beb-107">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span>

![](scaleout-with-redis/_static/image1.png)

<span data-ttu-id="66beb-108">对于本教程中，将使用三个服务器：</span><span class="sxs-lookup"><span data-stu-id="66beb-108">For this tutorial, you will use three servers:</span></span>

- <span data-ttu-id="66beb-109">两台运行 Windows，将使用的 SignalR 应用程序部署的服务器。</span><span class="sxs-lookup"><span data-stu-id="66beb-109">Two servers running Windows, which you will use to deploy a SignalR application.</span></span>
- <span data-ttu-id="66beb-110">一台运行 Linux，你将用于运行 Redis 服务器。</span><span class="sxs-lookup"><span data-stu-id="66beb-110">One server running Linux, which you will use to run Redis.</span></span> <span data-ttu-id="66beb-111">在本教程中的屏幕截图，我使用 Ubuntu 12.04 TLS。</span><span class="sxs-lookup"><span data-stu-id="66beb-111">For the screenshots in this tutorial, I used Ubuntu 12.04 TLS.</span></span>

<span data-ttu-id="66beb-112">如果没有要使用的三个物理服务器，您可以在 HYPER-V 上创建 Vm。</span><span class="sxs-lookup"><span data-stu-id="66beb-112">If you don't have three physical servers to use, you can create VMs on Hyper-V.</span></span> <span data-ttu-id="66beb-113">另一个选项是在 Azure 上创建 Vm。</span><span class="sxs-lookup"><span data-stu-id="66beb-113">Another option is to create VMs on Azure.</span></span>

<span data-ttu-id="66beb-114">虽然本教程使用官方 Redis 实现，此外，还有[Windows 端口的 Redis](https://github.com/MSOpenTech/redis)从 MSOpenTech。</span><span class="sxs-lookup"><span data-stu-id="66beb-114">Although this tutorial uses the official Redis implementation, there is also a [Windows port of Redis](https://github.com/MSOpenTech/redis) from MSOpenTech.</span></span> <span data-ttu-id="66beb-115">安装和配置不相同，但否则步骤是相同的。</span><span class="sxs-lookup"><span data-stu-id="66beb-115">Setup and configuration are different, but otherwise the steps are the same.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="66beb-116">使用 Redis 的 SignalR 横向扩展不支持 Redis 群集。</span><span class="sxs-lookup"><span data-stu-id="66beb-116">SignalR scaleout with Redis does not support Redis clusters.</span></span>


## <a name="overview"></a><span data-ttu-id="66beb-117">概述</span><span class="sxs-lookup"><span data-stu-id="66beb-117">Overview</span></span>

<span data-ttu-id="66beb-118">我们进入详细教程之前，下面是将执行哪些操作的快速概述。</span><span class="sxs-lookup"><span data-stu-id="66beb-118">Before we get to the detailed tutorial, here is a quick overview of what you will do.</span></span>

1. <span data-ttu-id="66beb-119">安装 Redis 并启动 Redis 服务器。</span><span class="sxs-lookup"><span data-stu-id="66beb-119">Install Redis and start the Redis server.</span></span>
2. <span data-ttu-id="66beb-120">将以下 NuGet 包添加到你的应用程序：</span><span class="sxs-lookup"><span data-stu-id="66beb-120">Add these NuGet packages to your application:</span></span> 

    - [<span data-ttu-id="66beb-121">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="66beb-121">Microsoft.AspNet.SignalR</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR)
    - [<span data-ttu-id="66beb-122">Microsoft.AspNet.SignalR.Redis</span><span class="sxs-lookup"><span data-stu-id="66beb-122">Microsoft.AspNet.SignalR.Redis</span></span>](http://nuget.org/packages/Microsoft.AspNet.SignalR.Redis)
3. <span data-ttu-id="66beb-123">创建 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="66beb-123">Create a SignalR application.</span></span>
4. <span data-ttu-id="66beb-124">将以下代码添加到 Global.asax 配置基架：</span><span class="sxs-lookup"><span data-stu-id="66beb-124">Add the following code to Global.asax to configure the backplane:</span></span> 

    [!code-csharp[Main](scaleout-with-redis/samples/sample1.cs)]

## <a name="ubuntu-on-hyper-v"></a><span data-ttu-id="66beb-125">Ubuntu 上的 HYPER-V</span><span class="sxs-lookup"><span data-stu-id="66beb-125">Ubuntu on Hyper-V</span></span>

<span data-ttu-id="66beb-126">使用 Windows 的 HYPER-V，您可以轻松创建 Ubuntu VM 在 Windows Server 上。</span><span class="sxs-lookup"><span data-stu-id="66beb-126">Using Windows Hyper-V, you can easily create an Ubuntu VM on Windows Server.</span></span>

<span data-ttu-id="66beb-127">下载从 Ubuntu ISO [ http://www.ubuntu.com ](http://www.ubuntu.com/)。</span><span class="sxs-lookup"><span data-stu-id="66beb-127">Download the Ubuntu ISO from [http://www.ubuntu.com](http://www.ubuntu.com/).</span></span>

<span data-ttu-id="66beb-128">在 HYPER-V 中添加新的 VM。</span><span class="sxs-lookup"><span data-stu-id="66beb-128">In Hyper-V, add a new VM.</span></span> <span data-ttu-id="66beb-129">在中**连接虚拟硬盘**步骤中，选择**创建虚拟硬盘**。</span><span class="sxs-lookup"><span data-stu-id="66beb-129">In the **Connect Virtual Hard Disk** step, select **Create a virtual hard disk**.</span></span>

![](scaleout-with-redis/_static/image2.png)

<span data-ttu-id="66beb-130">在中**安装选项**步骤中，选择**映像文件 (.iso)**，单击**浏览**，并浏览到 Ubuntu 安装 ISO。</span><span class="sxs-lookup"><span data-stu-id="66beb-130">In the **Installation Options** step, select **Image file (.iso)**, click **Browse**, and browse to the Ubuntu installation ISO.</span></span>

![](scaleout-with-redis/_static/image3.png)

## <a name="install-redis"></a><span data-ttu-id="66beb-131">安装 Redis</span><span class="sxs-lookup"><span data-stu-id="66beb-131">Install Redis</span></span>

<span data-ttu-id="66beb-132">遵循的步骤[ http://redis.io/download ](http://redis.io/download)以下载和生成 Redis。</span><span class="sxs-lookup"><span data-stu-id="66beb-132">Follow the steps at [http://redis.io/download](http://redis.io/download) to download and build Redis.</span></span>

[!code-console[Main](scaleout-with-redis/samples/sample2.cmd)]

<span data-ttu-id="66beb-133">这会生成 Redis 二进制文件`src`目录。</span><span class="sxs-lookup"><span data-stu-id="66beb-133">This builds the Redis binaries in the `src` directory.</span></span>

<span data-ttu-id="66beb-134">默认情况下，Redis 不需要密码。</span><span class="sxs-lookup"><span data-stu-id="66beb-134">By default, Redis does not require a password.</span></span> <span data-ttu-id="66beb-135">若要设置密码，请编辑`redis.conf`文件的源代码的根目录中。</span><span class="sxs-lookup"><span data-stu-id="66beb-135">To set a password, edit the `redis.conf` file, which is located in the root directory of the source code.</span></span> <span data-ttu-id="66beb-136">（请文件的备份副本之前对其进行编辑 ！）添加以下指令为`redis.conf`:</span><span class="sxs-lookup"><span data-stu-id="66beb-136">(Make a backup copy of the file before you edit it!) Add the following directive to `redis.conf`:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample3.ps1)]

<span data-ttu-id="66beb-137">现在，启动 Redis 服务器：</span><span class="sxs-lookup"><span data-stu-id="66beb-137">Now start the Redis server:</span></span>

[!code-css[Main](scaleout-with-redis/samples/sample4.css)]

![](scaleout-with-redis/_static/image4.png)

<span data-ttu-id="66beb-138">打开端口 6379，这是 Redis 的默认端口上侦听。</span><span class="sxs-lookup"><span data-stu-id="66beb-138">Open port 6379, which is the default port that Redis listens on.</span></span> <span data-ttu-id="66beb-139">（可以更改配置文件中的端口号。）</span><span class="sxs-lookup"><span data-stu-id="66beb-139">(You can change the port number in the configuration file.)</span></span>

## <a name="create-the-signalr-application"></a><span data-ttu-id="66beb-140">创建 SignalR 应用程序</span><span class="sxs-lookup"><span data-stu-id="66beb-140">Create the SignalR Application</span></span>

<span data-ttu-id="66beb-141">创建 SignalR 应用程序按照以下教程之一：</span><span class="sxs-lookup"><span data-stu-id="66beb-141">Create a SignalR application by following either of these tutorials:</span></span>

- [<span data-ttu-id="66beb-142">SignalR 入门</span><span class="sxs-lookup"><span data-stu-id="66beb-142">Getting Started with SignalR</span></span>](../getting-started/tutorial-getting-started-with-signalr.md)
- [<span data-ttu-id="66beb-143">使用 SignalR 和 MVC 4 入门</span><span class="sxs-lookup"><span data-stu-id="66beb-143">Getting Started with SignalR and MVC 4</span></span>](tutorial-getting-started-with-signalr-and-mvc-4.md)

<span data-ttu-id="66beb-144">接下来，我们将修改聊天应用程序，以支持采用 Redis 的扩展。</span><span class="sxs-lookup"><span data-stu-id="66beb-144">Next, we'll modify the chat application to support scaleout with Redis.</span></span> <span data-ttu-id="66beb-145">首先，将 SignalR.Redis NuGet 包添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="66beb-145">First, add the SignalR.Redis NuGet package to your project.</span></span> <span data-ttu-id="66beb-146">在 Visual Studio 中，从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="66beb-146">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="66beb-147">在包管理器控制台窗口中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="66beb-147">In the Package Manager Console window, enter the following command:</span></span>

[!code-powershell[Main](scaleout-with-redis/samples/sample5.ps1)]

<span data-ttu-id="66beb-148">接下来，打开 Global.asax 文件。</span><span class="sxs-lookup"><span data-stu-id="66beb-148">Next, open the Global.asax file.</span></span> <span data-ttu-id="66beb-149">将以下代码添加到**应用程序\_启动**方法：</span><span class="sxs-lookup"><span data-stu-id="66beb-149">Add the following code to the **Application\_Start** method:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample6.cs)]

- <span data-ttu-id="66beb-150">"服务器"是运行 Redis 服务器的名称。</span><span class="sxs-lookup"><span data-stu-id="66beb-150">"server" is the name of the server that is running Redis.</span></span>
- <span data-ttu-id="66beb-151">*端口*是端口号</span><span class="sxs-lookup"><span data-stu-id="66beb-151">*port* is the port number</span></span>
- <span data-ttu-id="66beb-152">"密码"是 redis.conf 文件中定义的密码。</span><span class="sxs-lookup"><span data-stu-id="66beb-152">"password" is the password that you defined in the redis.conf file.</span></span>
- <span data-ttu-id="66beb-153">"AppName"是任何字符串。</span><span class="sxs-lookup"><span data-stu-id="66beb-153">"AppName" is any string.</span></span> <span data-ttu-id="66beb-154">SignalR 具有此名称创建的 Redis 发布/订阅通道。</span><span class="sxs-lookup"><span data-stu-id="66beb-154">SignalR creates a Redis pub/sub channel with this name.</span></span>

<span data-ttu-id="66beb-155">例如：</span><span class="sxs-lookup"><span data-stu-id="66beb-155">For example:</span></span>

[!code-csharp[Main](scaleout-with-redis/samples/sample7.cs)]

## <a name="deploy-and-run-the-application"></a><span data-ttu-id="66beb-156">部署和运行应用程序</span><span class="sxs-lookup"><span data-stu-id="66beb-156">Deploy and Run the Application</span></span>

<span data-ttu-id="66beb-157">准备 Windows Server 实例将 SignalR 应用程序部署。</span><span class="sxs-lookup"><span data-stu-id="66beb-157">Prepare your Windows Server instances to deploy the SignalR application.</span></span>

<span data-ttu-id="66beb-158">添加 IIS 角色。</span><span class="sxs-lookup"><span data-stu-id="66beb-158">Add the IIS role.</span></span> <span data-ttu-id="66beb-159">包含"应用程序开发"功能，包括 WebSocket 协议。</span><span class="sxs-lookup"><span data-stu-id="66beb-159">Include "Application Development" features, including the WebSocket Protocol.</span></span>

![](scaleout-with-redis/_static/image5.png)

<span data-ttu-id="66beb-160">此外包括管理服务 （在"管理工具"列出）。</span><span class="sxs-lookup"><span data-stu-id="66beb-160">Also include the Management Service (listed under "Management Tools").</span></span>

![](scaleout-with-redis/_static/image6.png)

<span data-ttu-id="66beb-161">**安装 Web 部署 3.0。**</span><span class="sxs-lookup"><span data-stu-id="66beb-161">**Install Web Deploy 3.0.**</span></span> <span data-ttu-id="66beb-162">当你运行 IIS 管理器，它将提示你安装 Microsoft Web 平台，或者可以[下载 intstaller](https://go.microsoft.com/fwlink/?LinkId=255386)。</span><span class="sxs-lookup"><span data-stu-id="66beb-162">When you run IIS Manager, it will prompt you to install Microsoft Web Platform, or you can [download the intstaller](https://go.microsoft.com/fwlink/?LinkId=255386).</span></span> <span data-ttu-id="66beb-163">在平台安装程序中，搜索 Web 部署并安装 Web Deploy 3.0</span><span class="sxs-lookup"><span data-stu-id="66beb-163">In the Platform Installer, search for Web Deploy and install Web Deploy 3.0</span></span>

![](scaleout-with-redis/_static/image7.png)

<span data-ttu-id="66beb-164">检查 Web 管理服务正在运行。</span><span class="sxs-lookup"><span data-stu-id="66beb-164">Check that the Web Management Service is running.</span></span> <span data-ttu-id="66beb-165">如果没有，请启动服务。</span><span class="sxs-lookup"><span data-stu-id="66beb-165">If not, start the service.</span></span> <span data-ttu-id="66beb-166">（如果在 Windows 服务列表中，看不到 Web 管理服务，请确保添加 IIS 角色时安装管理服务。）</span><span class="sxs-lookup"><span data-stu-id="66beb-166">(If you don't see Web Management Service in the list of Windows services, make sure that you installed the Management Service when you added the IIS role.)</span></span>

<span data-ttu-id="66beb-167">默认情况下，Web 管理服务侦听 TCP 端口 8172。</span><span class="sxs-lookup"><span data-stu-id="66beb-167">By default, the Web Management Service listens on TCP port 8172.</span></span> <span data-ttu-id="66beb-168">在 Windows 防火墙中创建一个新的入站的规则以允许端口 8172 上的 TCP 流量。</span><span class="sxs-lookup"><span data-stu-id="66beb-168">In Windows Firewall, create a new inbound rule to allow TCP traffic on port 8172.</span></span> <span data-ttu-id="66beb-169">有关详细信息，请参阅[配置防火墙规则](https://technet.microsoft.com/library/dd448559(WS.10).aspx)。</span><span class="sxs-lookup"><span data-stu-id="66beb-169">For more information, see [Configuring Firewall Rules](https://technet.microsoft.com/library/dd448559(WS.10).aspx).</span></span> <span data-ttu-id="66beb-170">（如果托管在 Azure 上的 Vm，你可以直接在 Azure 门户中。</span><span class="sxs-lookup"><span data-stu-id="66beb-170">(If you are hosting the VMs on Azure, you can do this directly in the Azure portal.</span></span> <span data-ttu-id="66beb-171">请参阅[如何设置虚拟机的终结点](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/)。)</span><span class="sxs-lookup"><span data-stu-id="66beb-171">See [How to Set Up Endpoints to a Virtual Machine](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/).)</span></span>

<span data-ttu-id="66beb-172">现在已准备好部署到服务器从开发计算机的 Visual Studio 项目。</span><span class="sxs-lookup"><span data-stu-id="66beb-172">Now you are ready to deploy the Visual Studio project from your development machine to the server.</span></span> <span data-ttu-id="66beb-173">在解决方案资源管理器，右键单击解决方案，然后单击**发布**。</span><span class="sxs-lookup"><span data-stu-id="66beb-173">In Solution Explorer, right-click the solution and click **Publish**.</span></span>

<span data-ttu-id="66beb-174">有关更详细的有关 web 部署的文档，请参阅[用于 Visual Studio 和 ASP.NET 的 Web 部署内容映射](../../../whitepapers/aspnet-web-deployment-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="66beb-174">For more detailed documentation about web deployment, see [Web Deployment Content Map for Visual Studio and ASP.NET](../../../whitepapers/aspnet-web-deployment-content-map.md).</span></span>

<span data-ttu-id="66beb-175">如果部署到两个服务器应用程序，可以在一个单独的浏览器窗口中打开每个实例，并查看它们每个接收来自其他 SignalR 消息。</span><span class="sxs-lookup"><span data-stu-id="66beb-175">If you deploy the application to two servers, you can open each instance in a separate browser window and see that they each receive SignalR messages from the other.</span></span> <span data-ttu-id="66beb-176">（当然，在生产环境中，两个服务器将位于负载均衡器后面。）</span><span class="sxs-lookup"><span data-stu-id="66beb-176">(Of course, in a production environment, the two servers would sit behind a load balancer.)</span></span>

![](scaleout-with-redis/_static/image8.png)

<span data-ttu-id="66beb-177">如果您感兴趣，查看将发送到 Redis，可以使用的消息**redis cli**安装使用 Redis 的客户端。</span><span class="sxs-lookup"><span data-stu-id="66beb-177">If you're curious to see the messages that are sent to Redis, you can use the **redis-cli** client, which installs with Redis.</span></span>

[!code-xml[Main](scaleout-with-redis/samples/sample8.xml)]

![](scaleout-with-redis/_static/image9.png)
