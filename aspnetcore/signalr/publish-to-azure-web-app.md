---
title: 发布 ASP.NET Core SignalR 应用程序到 Azure 应用服务
author: bradygaster
description: 了解如何将 ASP.NET Core SignalR 应用发布到 Azure 应用服务。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406104"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="3f769-103">发布 ASP.NET Core SignalR 应用程序到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="3f769-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="3f769-104">通过[Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="3f769-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="3f769-105">[Azure 应用服务](/azure/app-service/app-service-web-overview)是[Microsoft 云计算](https://azure.microsoft.com/)平台服务，用于托管 web 应用，包括 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="3f769-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="3f769-106">本文是指从 Visual Studio 发布 ASP.NET Core SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="3f769-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="3f769-107">有关详细信息，请参阅[Azure SignalR 服务](https://azure.microsoft.com/services/signalr-service)。</span><span class="sxs-lookup"><span data-stu-id="3f769-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="3f769-108">发布应用</span><span class="sxs-lookup"><span data-stu-id="3f769-108">Publish the app</span></span>

<span data-ttu-id="3f769-109">本文介绍如何发布使用 Visual Studio 中的工具。</span><span class="sxs-lookup"><span data-stu-id="3f769-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="3f769-110">可以使用 visual Studio Code 用户[Azure CLI](/cli/azure)命令以将应用发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="3f769-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="3f769-111">有关详细信息，请参阅[使用命令行工具将 ASP.NET Core 应用发布到 Azure](/azure/app-service/app-service-web-get-started-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="3f769-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="3f769-112">在“解决方案资源管理器”  中右键单击该项目，然后选择“发布”  。</span><span class="sxs-lookup"><span data-stu-id="3f769-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="3f769-113">确认**应用服务**并**新建**中选择**选取发布目标**对话框。</span><span class="sxs-lookup"><span data-stu-id="3f769-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="3f769-114">选择**创建配置文件**从**发布**向下按钮拖放。</span><span class="sxs-lookup"><span data-stu-id="3f769-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="3f769-115">输入中的以下表中所述的信息**创建应用服务**对话框并选择**创建**。</span><span class="sxs-lookup"><span data-stu-id="3f769-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="3f769-116">项</span><span class="sxs-lookup"><span data-stu-id="3f769-116">Item</span></span>               | <span data-ttu-id="3f769-117">描述</span><span class="sxs-lookup"><span data-stu-id="3f769-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="3f769-118">**名称**</span><span class="sxs-lookup"><span data-stu-id="3f769-118">**Name**</span></span>           | <span data-ttu-id="3f769-119">应用程序的唯一名称。</span><span class="sxs-lookup"><span data-stu-id="3f769-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="3f769-120">**订阅**</span><span class="sxs-lookup"><span data-stu-id="3f769-120">**Subscription**</span></span>   | <span data-ttu-id="3f769-121">该应用使用的 azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="3f769-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="3f769-122">**资源组**</span><span class="sxs-lookup"><span data-stu-id="3f769-122">**Resource Group**</span></span> | <span data-ttu-id="3f769-123">应用程序所属的相关资源的组。</span><span class="sxs-lookup"><span data-stu-id="3f769-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="3f769-124">**托管计划**</span><span class="sxs-lookup"><span data-stu-id="3f769-124">**Hosting Plan**</span></span>   | <span data-ttu-id="3f769-125">Web 应用的的定价计划。</span><span class="sxs-lookup"><span data-stu-id="3f769-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="3f769-126">选择**Azure SignalR 服务**中**依赖关系** > **添加**下拉列表：</span><span class="sxs-lookup"><span data-stu-id="3f769-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   ![添加下拉列表中显示所选内容的 Azure SignalR 服务的依赖项区域](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="3f769-128">在中**Azure SignalR 服务**对话框中，选择**创建新的 Azure SignalR 服务实例**。</span><span class="sxs-lookup"><span data-stu-id="3f769-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="3f769-129">提供**名称**，**资源组**，并**位置**。</span><span class="sxs-lookup"><span data-stu-id="3f769-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="3f769-130">返回到**Azure SignalR 服务**对话框并选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="3f769-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="3f769-131">Visual Studio 将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="3f769-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="3f769-132">创建发布配置文件包含发布设置。</span><span class="sxs-lookup"><span data-stu-id="3f769-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="3f769-133">创建*的 Azure Web 应用*与提供的详细信息。</span><span class="sxs-lookup"><span data-stu-id="3f769-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="3f769-134">会将应用发布。</span><span class="sxs-lookup"><span data-stu-id="3f769-134">Publishes the app.</span></span>
* <span data-ttu-id="3f769-135">将启动浏览器中，这将加载 web 应用。</span><span class="sxs-lookup"><span data-stu-id="3f769-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="3f769-136">应用程序的 URL 的格式是`{APP SERVICE NAME}.azurewebsites.net`。</span><span class="sxs-lookup"><span data-stu-id="3f769-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="3f769-137">例如，名为的应用`SignalRChatApp`具有的 URL `https://signalrchatapp.azurewebsites.net`。</span><span class="sxs-lookup"><span data-stu-id="3f769-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="3f769-138">如果 HTTP *502.2-错误的网关*部署面向预览版.NET Core 发行版的应用时出现错误，请参阅[到 Azure 应用服务部署 ASP.NET Core 预览版本](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)解决方法。</span><span class="sxs-lookup"><span data-stu-id="3f769-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="3f769-139">在 Azure 应用服务中配置应用</span><span class="sxs-lookup"><span data-stu-id="3f769-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="3f769-140">*本部分仅适用于不使用 Azure SignalR 服务应用。*</span><span class="sxs-lookup"><span data-stu-id="3f769-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="3f769-141">如果应用使用 Azure SignalR 服务，应用服务不需要应用程序请求路由 (ARR) 关联和 Web 套接字在本部分中所述的配置。</span><span class="sxs-lookup"><span data-stu-id="3f769-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="3f769-142">Azure SignalR 服务，而不是直接应用程序，客户端连接其 Web 套接字。</span><span class="sxs-lookup"><span data-stu-id="3f769-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="3f769-143">对于托管的应用而无需 Azure SignalR 服务，启用：</span><span class="sxs-lookup"><span data-stu-id="3f769-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="3f769-144">[ARR 相关性](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html)到回相同的应用服务实例路由来自用户的请求。</span><span class="sxs-lookup"><span data-stu-id="3f769-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="3f769-145">默认设置是**上**。</span><span class="sxs-lookup"><span data-stu-id="3f769-145">The default setting is **On**.</span></span>
* <span data-ttu-id="3f769-146">[Web 套接字](xref:fundamentals/websockets)以允许 Web 套接字传输到该函数。</span><span class="sxs-lookup"><span data-stu-id="3f769-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="3f769-147">默认设置是**关闭**。</span><span class="sxs-lookup"><span data-stu-id="3f769-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="3f769-148">在 Azure 门户中，导航到中的 web 应用**应用服务**。</span><span class="sxs-lookup"><span data-stu-id="3f769-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="3f769-149">打开**配置** > **常规设置**。</span><span class="sxs-lookup"><span data-stu-id="3f769-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="3f769-150">设置**Web 套接字**到**上**。</span><span class="sxs-lookup"><span data-stu-id="3f769-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="3f769-151">确认**ARR 相关性**设置为**上**。</span><span class="sxs-lookup"><span data-stu-id="3f769-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="3f769-152">应用服务计划限制</span><span class="sxs-lookup"><span data-stu-id="3f769-152">App Service Plan limits</span></span>

<span data-ttu-id="3f769-153">Web 套接字和其他传输是限制基于所选的应用服务计划。</span><span class="sxs-lookup"><span data-stu-id="3f769-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="3f769-154">有关详细信息，请参阅*Azure 云服务限制*并*应用服务限制*的部分[Azure 订阅和服务限制、 配额和约束](/azure/azure-subscription-service-limits#app-service-limits)一文。</span><span class="sxs-lookup"><span data-stu-id="3f769-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f769-155">其他资源</span><span class="sxs-lookup"><span data-stu-id="3f769-155">Additional resources</span></span>

* [<span data-ttu-id="3f769-156">什么是 Azure SignalR 服务？</span><span class="sxs-lookup"><span data-stu-id="3f769-156">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="3f769-157">使用命令行工具将 ASP.NET Core 应用发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="3f769-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="3f769-158">承载并将其部署在 Azure 上的 ASP.NET Core 预览应用</span><span class="sxs-lookup"><span data-stu-id="3f769-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
