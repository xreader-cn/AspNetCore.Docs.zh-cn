---
uid: signalr/overview/deployment/using-signalr-with-azure-web-sites
title: 在 Azure 应用服务中的 Web 应用中使用 SignalR |Microsoft Docs
author: bradygaster
description: 本文档介绍如何配置运行在 Microsoft Azure 的 SignalR 应用程序。 在教程的 Visual Studio 2013 或 Vis.中使用的软件版本...
ms.author: bradyg
ms.date: 07/01/2015
ms.assetid: 2a7517a0-b88c-4162-ade3-9bf6ca7062fd
msc.legacyurl: /signalr/overview/deployment/using-signalr-with-azure-web-sites
msc.type: authoredcontent
ms.openlocfilehash: 13eb5d29a2c40f52aed4b569ec8695f014a05f03
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837697"
---
<a name="using-signalr-with-web-apps-in-azure-app-service"></a><span data-ttu-id="1bc0a-104">在 Azure 应用服务中的 Web 应用中使用 SignalR</span><span class="sxs-lookup"><span data-stu-id="1bc0a-104">Using SignalR with Web Apps in Azure App Service</span></span>
====================
<span data-ttu-id="1bc0a-105">通过[Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="1bc0a-105">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="1bc0a-106">本文档介绍如何配置运行在 Microsoft Azure 的 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-106">This document describes how to configure a SignalR application that runs on Microsoft Azure.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="1bc0a-107">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="1bc0a-107">Software versions used in the tutorial</span></span>
>
>
> - <span data-ttu-id="1bc0a-108">[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)或 Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="1bc0a-108">[Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013) or Visual Studio 2012</span></span>
> - <span data-ttu-id="1bc0a-109">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="1bc0a-109">.NET 4.5</span></span>
> - <span data-ttu-id="1bc0a-110">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="1bc0a-110">SignalR version 2</span></span>
> - <span data-ttu-id="1bc0a-111">用于 Visual Studio 2013 或 2012年的 azure SDK 2.3</span><span class="sxs-lookup"><span data-stu-id="1bc0a-111">Azure SDK 2.3 for Visual Studio 2013 or 2012</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="1bc0a-112">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="1bc0a-112">Questions and comments</span></span>
>
> <span data-ttu-id="1bc0a-113">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="1bc0a-114">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)， [StackOverflow.com](http://stackoverflow.com/)，或[Microsoft Azure 论坛](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).</span><span class="sxs-lookup"><span data-stu-id="1bc0a-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR), [StackOverflow.com](http://stackoverflow.com/), or the [Microsoft Azure forums](https://social.msdn.microsoft.com/Forums/windowsazure/home?category=windowsazureplatform).</span></span>


## <a name="table-of-contents"></a><span data-ttu-id="1bc0a-115">目录</span><span class="sxs-lookup"><span data-stu-id="1bc0a-115">Table of Contents</span></span>

- [<span data-ttu-id="1bc0a-116">介绍</span><span class="sxs-lookup"><span data-stu-id="1bc0a-116">Introduction</span></span>](#introduction)
- [<span data-ttu-id="1bc0a-117">SignalR Web 应用部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="1bc0a-117">Deploying a SignalR Web App to Azure App Service</span></span>](#deploying)
- [<span data-ttu-id="1bc0a-118">Azure 应用服务上启用 Websocket</span><span class="sxs-lookup"><span data-stu-id="1bc0a-118">Enabling WebSockets on Azure App Service</span></span>](#websocket)
- [<span data-ttu-id="1bc0a-119">使用 Azure Redis 缓存底板</span><span class="sxs-lookup"><span data-stu-id="1bc0a-119">Using the Azure Redis Cache Backplane</span></span>](#backplane)
- [<span data-ttu-id="1bc0a-120">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1bc0a-120">Next Steps</span></span>](#nextsteps)

<a id="introduction"></a>
## <a name="introduction"></a><span data-ttu-id="1bc0a-121">介绍</span><span class="sxs-lookup"><span data-stu-id="1bc0a-121">Introduction</span></span>

<span data-ttu-id="1bc0a-122">可以使用 ASP.NET SignalR 将一个新的服务器和 web 或.NET 客户端之间的交互级别。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-122">ASP.NET SignalR can be used to bring a new level of interactivity between servers and web or .NET clients.</span></span> <span data-ttu-id="1bc0a-123">Azure 中托管时，SignalR 应用程序可以充分利用高度可用、 可缩放，并在云中运行的提供高性能环境。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-123">When hosted in Azure, SignalR applications can take advantage of the highly available, scalable, and performant environment that running in the cloud provides.</span></span>

<a id="deploying"></a>
## <a name="deploying-a-signalr-web-app-to-azure-app-service"></a><span data-ttu-id="1bc0a-124">SignalR Web 应用部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="1bc0a-124">Deploying a SignalR Web App to Azure App Service</span></span>

<span data-ttu-id="1bc0a-125">SignalR 不会添加到 Azure 的应用程序部署与部署到的本地服务器的任何特定的复杂情况。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-125">SignalR doesn't add any particular complications to deploying an application to Azure versus deploying to an on-premises server.</span></span> <span data-ttu-id="1bc0a-126">可以在 Azure 中无需更改任何配置或其他设置中托管的应用程序使用 SignalR (但 Websocket 支持，请参阅[Azure 应用服务上启用 WebSockets](#websocket)下面。)对于本教程中，您需要将中创建的应用程序部署[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-126">An application that uses SignalR can be hosted in Azure without any changes in configuration or other settings (though for WebSockets support, see [Enabling WebSockets on Azure App Service](#websocket) below.) For this tutorial, you'll deploy the application created in the [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md) to Azure.</span></span>

<span data-ttu-id="1bc0a-127">**系统必备**</span><span class="sxs-lookup"><span data-stu-id="1bc0a-127">**Prerequisites**</span></span>

- <span data-ttu-id="1bc0a-128">Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="1bc0a-128">Visual Studio 2013.</span></span> <span data-ttu-id="1bc0a-129">如果未安装 Visual Studio，Visual Studio 2013 Express for Web 是在安装中包括的 Azure sdk。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-129">If you don't have Visual Studio, Visual Studio 2013 Express for Web is included in the install of the Azure SDK.</span></span>
- <span data-ttu-id="1bc0a-130">[用于 Visual Studio 2013 的 azure SDK 2.3](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)或[适用于 Visual Studio 2012 的 Azure SDK 2.3](https://go.microsoft.com/fwlink/p/?linkid=323511)。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-130">[Azure SDK 2.3 for Visual Studio 2013](https://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409) or [Azure SDK 2.3 for Visual Studio 2012](https://go.microsoft.com/fwlink/p/?linkid=323511).</span></span>
- <span data-ttu-id="1bc0a-131">若要完成本教程中，将需要 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-131">To complete this tutorial, you will need an Azure subscription.</span></span> <span data-ttu-id="1bc0a-132">你可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，或[注册试用版订阅](https://azure.microsoft.com/pricing/free-trial/)。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-132">You can [activate your MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), or [sign up for a trial subscription](https://azure.microsoft.com/pricing/free-trial/).</span></span>

### <a name="deploying-a-signalr-web-app-to-azure"></a><span data-ttu-id="1bc0a-133">SignalR web 应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="1bc0a-133">Deploying a SignalR web app to Azure</span></span>

1. <span data-ttu-id="1bc0a-134">完成[入门教程](../getting-started/tutorial-getting-started-with-signalr.md)，或下载中完成的项目[代码库](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9)。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-134">Complete the [Getting Started Tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the finished project from [Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
2. <span data-ttu-id="1bc0a-135">在 Visual Studio 中，选择**构建**，**发布 SignalR 聊天**。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-135">In Visual Studio, select **Build**, **Publish SignalR Chat**.</span></span>
3. <span data-ttu-id="1bc0a-136">在"发布 Web"对话框中，选择"Windows Azure 网站"。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-136">In the "Publish Web" dialog, select "Windows Azure Web Sites".</span></span>

    ![选择 Azure 网站](using-signalr-with-azure-web-sites/_static/image1.png)
4. <span data-ttu-id="1bc0a-138">如果您没有登录到你的 Microsoft 帐户，请单击**登录...** 中"选择现有网站"对话框中，并登录。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-138">If you aren't signed in to your Microsoft account, click **Sign In...** in the "Select Existing Web Site" dialog, and sign in.</span></span>

    ![选择现有网站](using-signalr-with-azure-web-sites/_static/image2.png)    ![登录 Azure](using-signalr-with-azure-web-sites/_static/image3.png)
5. <span data-ttu-id="1bc0a-141">在"选择现有网站"对话框中，单击**新建**。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-141">In the "Select Existing Web Site" dialog, click **New**.</span></span>

    ![新建网站](using-signalr-with-azure-web-sites/_static/image4.png)
6. <span data-ttu-id="1bc0a-143">在"Windows Azure 上的创建网站"对话框中，输入唯一的应用名称。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-143">In the "Create site on Windows Azure" dialog, enter a unique app name.</span></span> <span data-ttu-id="1bc0a-144">在区域下拉列表中选择离你最近的区域。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-144">Select the region closest to you in the Region dropdown.</span></span> <span data-ttu-id="1bc0a-145">单击 **“创建”**。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-145">Click **Create**.</span></span>

    ![在 Azure 中创建网站](using-signalr-with-azure-web-sites/_static/image5.png)
7. <span data-ttu-id="1bc0a-147">在"发布 Web"对话框中，单击**发布**。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-147">In the "Publish Web" dialog, click **Publish**.</span></span>

    ![发布站点](using-signalr-with-azure-web-sites/_static/image6.png)
8. <span data-ttu-id="1bc0a-149">应用完成发布后，将在浏览器中打开托管在 Azure 应用服务 Web 应用中的 SignalR 聊天应用程序。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-149">When the app has completed publishing, the SignalR Chat application hosted in Azure App Service Web Apps will open in a browser.</span></span>

    ![在浏览器中打开站点](using-signalr-with-azure-web-sites/_static/image7.png)

<a id="websocket"></a>
### <a name="enabling-websockets-on-azure-app-service-web-apps"></a><span data-ttu-id="1bc0a-151">在 Azure 应用服务 Web 应用启用 Websocket</span><span class="sxs-lookup"><span data-stu-id="1bc0a-151">Enabling WebSockets on Azure App Service Web Apps</span></span>

<span data-ttu-id="1bc0a-152">Websocket 需要显式启用 web 应用程序中使用 SignalR 应用程序; 中否则，将使用其他协议 (请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)有关详细信息)。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-152">WebSockets needs to be explicitly enabled in your web app to be used in a SignalR application; otherwise, other protocols will be used (See [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports) for details).</span></span>

<span data-ttu-id="1bc0a-153">若要在 Azure 应用服务 Web 应用使用 Websocket，启用它的 web 应用的配置部分中。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-153">In order to use WebSockets on Azure App Service Web Apps, enable it in the configuration section of the web app.</span></span> <span data-ttu-id="1bc0a-154">若要执行此操作，请打开中的 web 应用[Azure 管理门户](https://manage.windowsazure.com/)，并选择配置。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-154">To do this, open your web app in the [Azure Management Portal](https://manage.windowsazure.com/), and select Configure.</span></span>

![配置选项卡](using-signalr-with-azure-web-sites/_static/image8.png)

<span data-ttu-id="1bc0a-156">在配置页的顶部，请确保你的 web 应用，使用.NET 4.5。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-156">At the top of the configuration page, ensure that .NET 4.5 is used for your web app.</span></span>

![.NET framework 版本 4.5 设置](using-signalr-with-azure-web-sites/_static/image9.png)

<span data-ttu-id="1bc0a-158">在配置页上，在**Websocket**设置中，选择**上**。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-158">On the configuration page, in the **WebSockets** setting, select **On**.</span></span>

![Websocket 的设置：On](using-signalr-with-azure-web-sites/_static/image10.png)

<span data-ttu-id="1bc0a-160">在配置页的底部，选择**保存**以保存所做的更改。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-160">At the bottom of the Configuration page, select **Save** to save your changes.</span></span>

![保存设置](using-signalr-with-azure-web-sites/_static/image11.png)

<a id="backplane"></a>
## <a name="using-the-azure-redis-cache-backplane"></a><span data-ttu-id="1bc0a-162">使用 Azure Redis 缓存底板</span><span class="sxs-lookup"><span data-stu-id="1bc0a-162">Using the Azure Redis Cache Backplane</span></span>

<span data-ttu-id="1bc0a-163">如果为 web 应用，使用多个实例，这些实例的用户需要彼此交互，以便，例如，创建一个实例中的聊天消息可以访问用户连接到其他实例），则[Azure Redis 缓存底板](../performance/scaleout-with-redis.md)必须在你的应用程序中实现。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-163">If you use multiple instances for your web app, and the users of those instances need to interact with one another (so that, for instance, chat messages created in one instance can reach the users connected to other instances), the [Azure Redis Cache backplane](../performance/scaleout-with-redis.md) must be implemented in your application.</span></span>

<a id="nextsteps"></a>
## <a name="next-steps"></a><span data-ttu-id="1bc0a-164">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1bc0a-164">Next Steps</span></span>

<span data-ttu-id="1bc0a-165">在 Azure 应用服务中 Web 应用的详细信息，请参阅[Web 应用概述](https://azure.microsoft.com/documentation/articles/app-service-web-overview/)。</span><span class="sxs-lookup"><span data-stu-id="1bc0a-165">For more information on Web Apps in Azure App Service, see [Web Apps overview](https://azure.microsoft.com/documentation/articles/app-service-web-overview/).</span></span>
