---
title: 发布 ASP.NET Core SignalR 应用程序到 Azure Web 应用
author: bradygaster
description: 发布 ASP.NET Core SignalR 应用程序到 Azure Web 应用
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 66fa855590c49c4284e4b42cae57f3d4d81dd0fc
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837671"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a><span data-ttu-id="f8223-103">发布 ASP.NET Core SignalR 应用程序到 Azure Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="f8223-103">Publish an ASP.NET Core SignalR app to an Azure Web App</span></span>

<span data-ttu-id="f8223-104">[Azure Web 应用程序](/azure/app-service/app-service-web-overview)是[Microsoft 云计算](https://azure.microsoft.com/)用于承载 web 应用，包括 ASP.NET Core 平台服务。</span><span class="sxs-lookup"><span data-stu-id="f8223-104">[Azure Web App](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f8223-105">本文是指从 Visual Studio 发布 ASP.NET Core SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8223-105">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="f8223-106">请访问[Azure SignalR 服务](https://azure.microsoft.com/en-gb/services/signalr-service?)有关如何在 Azure 上使用 SignalR 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f8223-106">Visit [SignalR service for Azure](https://azure.microsoft.com/en-gb/services/signalr-service?) for more information about using SignalR on Azure.</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="f8223-107">发布应用</span><span class="sxs-lookup"><span data-stu-id="f8223-107">Publish the app</span></span>

<span data-ttu-id="f8223-108">Visual Studio 提供的内置工具发布到 Azure Web 应用。</span><span class="sxs-lookup"><span data-stu-id="f8223-108">Visual Studio provides built-in tools for publishing to an Azure Web App.</span></span> <span data-ttu-id="f8223-109">可以使用 visual Studio Code 用户[Azure CLI](/cli/azure)命令以将应用发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="f8223-109">Visual Studio Code user can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="f8223-110">本文介绍如何发布使用 Visual Studio 中的工具。</span><span class="sxs-lookup"><span data-stu-id="f8223-110">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="f8223-111">若要发布应用程序使用 Azure CLI，请参阅[将 ASP.NET Core 应用发布到 Azure 中，使用命令行工具](/azure/app-service/app-service-web-get-started-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="f8223-111">To publish an app using Azure CLI, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

<span data-ttu-id="f8223-112">右键单击该项目中**解决方案资源管理器**，然后选择**发布**。</span><span class="sxs-lookup"><span data-stu-id="f8223-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span> <span data-ttu-id="f8223-113">确认**新建**签入**选取发布目标**对话框中，然后选择**发布**。</span><span class="sxs-lookup"><span data-stu-id="f8223-113">Confirm that **Create new** is checked in the **Pick a publish target** dialog, and select **Publish**.</span></span>

![选取发布目标](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

<span data-ttu-id="f8223-115">输入中的以下信息**创建应用服务**对话框并选择**创建**。</span><span class="sxs-lookup"><span data-stu-id="f8223-115">Enter the following information in the **Create App Service** dialog and select **Create**.</span></span>

| <span data-ttu-id="f8223-116">项</span><span class="sxs-lookup"><span data-stu-id="f8223-116">Item</span></span> | <span data-ttu-id="f8223-117">描述</span><span class="sxs-lookup"><span data-stu-id="f8223-117">Description</span></span> |
| ---- | ----------- |
| <span data-ttu-id="f8223-118">**应用名称**</span><span class="sxs-lookup"><span data-stu-id="f8223-118">**App name**</span></span> | <span data-ttu-id="f8223-119">应用的唯一名称。</span><span class="sxs-lookup"><span data-stu-id="f8223-119">A unique name of the app.</span></span> |
| <span data-ttu-id="f8223-120">**订阅**</span><span class="sxs-lookup"><span data-stu-id="f8223-120">**Subscription**</span></span> | <span data-ttu-id="f8223-121">该应用使用 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="f8223-121">The Azure subscription that the app uses.</span></span> |
| <span data-ttu-id="f8223-122">**资源组**</span><span class="sxs-lookup"><span data-stu-id="f8223-122">**Resource Group**</span></span> | <span data-ttu-id="f8223-123">应用程序所属的相关资源的组。</span><span class="sxs-lookup"><span data-stu-id="f8223-123">The group of related resources to which the app belongs.</span></span>  |
| <span data-ttu-id="f8223-124">**托管计划**</span><span class="sxs-lookup"><span data-stu-id="f8223-124">**Hosting Plan**</span></span> | <span data-ttu-id="f8223-125">用于 web 应用的定价计划。</span><span class="sxs-lookup"><span data-stu-id="f8223-125">The pricing plan for the web app.</span></span> |

![创建应用服务](publish-to-azure-web-app/_static/create-app-service-dialog.png)

<span data-ttu-id="f8223-127">Visual Studio 将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="f8223-127">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="f8223-128">创建发布配置文件包含发布设置。</span><span class="sxs-lookup"><span data-stu-id="f8223-128">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="f8223-129">创建或使用现有*的 Azure Web 应用*与提供的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f8223-129">Creates or uses an existing *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="f8223-130">会将应用发布。</span><span class="sxs-lookup"><span data-stu-id="f8223-130">Publishes the app.</span></span>
* <span data-ttu-id="f8223-131">将浏览器中，启动已发布的 web 应用程序加载。</span><span class="sxs-lookup"><span data-stu-id="f8223-131">Launches a browser, with the published web app loaded.</span></span>

<span data-ttu-id="f8223-132">请注意 URL 的格式，应用程序 *{应用名称}.azurewebsites.net.net*。</span><span class="sxs-lookup"><span data-stu-id="f8223-132">Notice the format of the URL for the app is *{app name}.azurewebsites.net*.</span></span> <span data-ttu-id="f8223-133">例如，名为的应用`SignalRChattR`具有如下所示的 URL `https://signalrchattr.azurewebsites.net`。</span><span class="sxs-lookup"><span data-stu-id="f8223-133">For example, an app named `SignalRChattR` has a URL that looks like `https://signalrchattr.azurewebsites.net`.</span></span>

<span data-ttu-id="f8223-134">如果发生 HTTP 502.2 错误，请参阅[到 Azure App Service 部署 ASP.NET Core 预览版](xref:host-and-deploy/azure-apps/index)来解决。</span><span class="sxs-lookup"><span data-stu-id="f8223-134">If an HTTP 502.2 error occurs, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index) to resolve it.</span></span>

## <a name="configure-signalr-web-app"></a><span data-ttu-id="f8223-135">SignalR web 应用配置</span><span class="sxs-lookup"><span data-stu-id="f8223-135">Configure SignalR web app</span></span>

<span data-ttu-id="f8223-136">ASP.NET Core SignalR 应用程序作为 Azure Web 应用必须发布[ARR 地缘](https://en.wikipedia.org/wiki/Application_Request_Routing)启用。</span><span class="sxs-lookup"><span data-stu-id="f8223-136">ASP.NET Core SignalR apps that are published as an Azure Web App must have [ARR Affinity](https://en.wikipedia.org/wiki/Application_Request_Routing) enabled.</span></span> <span data-ttu-id="f8223-137">[Websocket](xref:fundamentals/websockets)应启用，以允许 Websocket 传输到该函数。</span><span class="sxs-lookup"><span data-stu-id="f8223-137">[WebSockets](xref:fundamentals/websockets) should be enabled, to allow the WebSockets transport to function.</span></span>

<span data-ttu-id="f8223-138">在 Azure 门户中，导航到**应用设置**为 web 应用。</span><span class="sxs-lookup"><span data-stu-id="f8223-138">In the Azure portal, navigate to **App Settings** for your web app.</span></span> <span data-ttu-id="f8223-139">设置**Websocket**到**上**，并验证**ARR 相关性**是**上**。</span><span class="sxs-lookup"><span data-stu-id="f8223-139">Set **WebSockets** to **On**, and verify **ARR Affinity** is **On**.</span></span>

![在 Azure 门户中的 azure Web 应用设置](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 <span data-ttu-id="f8223-141">Websocket 和其他传输[都是有限的应用服务计划上基于](/azure/azure-subscription-service-limits#app-service-limits)。</span><span class="sxs-lookup"><span data-stu-id="f8223-141">WebSockets and other transports [are limited based on the App Service Plan](/azure/azure-subscription-service-limits#app-service-limits).</span></span>

## <a name="related-resources"></a><span data-ttu-id="f8223-142">相关资源</span><span class="sxs-lookup"><span data-stu-id="f8223-142">Related resources</span></span>

* [<span data-ttu-id="f8223-143">使用命令行工具将 ASP.NET Core 应用发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="f8223-143">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="f8223-144">向具有 Visual Studio 的 Azure 发布 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="f8223-144">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>](xref:tutorials/publish-to-azure-webapp-using-vs)
* [<span data-ttu-id="f8223-145">承载并将其部署在 Azure 上的 ASP.NET Core 预览应用</span><span class="sxs-lookup"><span data-stu-id="f8223-145">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
