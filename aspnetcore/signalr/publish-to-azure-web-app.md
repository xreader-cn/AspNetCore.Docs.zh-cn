---
title: :::no-loc(SignalR):::将 ASP.NET Core 应用程序发布到 Azure App Service
author: bradygaster
description: '了解如何将 ASP.NET Core :::no-loc(SignalR)::: 应用程序发布到 Azure App Service。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234500"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="d4905-103">:::no-loc(SignalR):::将 ASP.NET Core 应用程序发布到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d4905-103">Publish an ASP.NET Core :::no-loc(SignalR)::: app to Azure App Service</span></span>

<span data-ttu-id="d4905-104">作者： [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="d4905-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="d4905-105">[Azure App Service](/azure/app-service/app-service-web-overview) 是一种 [Microsoft 云计算](https://azure.microsoft.com/) 平台服务，用于承载 web 应用，包括 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="d4905-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d4905-106">本文介绍如何 :::no-loc(SignalR)::: 从 Visual Studio 发布 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="d4905-106">This article refers to publishing an ASP.NET Core :::no-loc(SignalR)::: app from Visual Studio.</span></span> <span data-ttu-id="d4905-107">有关详细信息，请参阅[ :::no-loc(SignalR)::: Azure 服务](https://azure.microsoft.com/services/signalr-service)。</span><span class="sxs-lookup"><span data-stu-id="d4905-107">For more information, see [:::no-loc(SignalR)::: service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="d4905-108">发布应用</span><span class="sxs-lookup"><span data-stu-id="d4905-108">Publish the app</span></span>

<span data-ttu-id="d4905-109">本文介绍如何使用 Visual Studio 中的工具进行发布。</span><span class="sxs-lookup"><span data-stu-id="d4905-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="d4905-110">Visual Studio Code 用户可以使用 [Azure CLI](/cli/azure) 命令将应用发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="d4905-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="d4905-111">有关详细信息，请参阅 [使用命令行工具将 ASP.NET Core 应用程序发布到 Azure](/azure/app-service/app-service-web-get-started-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="d4905-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="d4905-112">在“解决方案资源管理器”中右键单击该项目，然后选择“发布”。</span><span class="sxs-lookup"><span data-stu-id="d4905-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="d4905-113">确认已在 " **选取发布目标** " 对话框中选择 " **应用服务** " 和 " **新建** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="d4905-114">从 " **发布** " 按钮下拉菜单中选择 " **创建配置文件** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="d4905-115">在 " **创建应用服务** " 对话框中，输入下表中所述的信息，然后选择 " **创建** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="d4905-116">项目</span><span class="sxs-lookup"><span data-stu-id="d4905-116">Item</span></span>               | <span data-ttu-id="d4905-117">说明</span><span class="sxs-lookup"><span data-stu-id="d4905-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="d4905-118">**名称**</span><span class="sxs-lookup"><span data-stu-id="d4905-118">**Name**</span></span>           | <span data-ttu-id="d4905-119">应用的唯一名称。</span><span class="sxs-lookup"><span data-stu-id="d4905-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="d4905-120">**订阅**</span><span class="sxs-lookup"><span data-stu-id="d4905-120">**Subscription**</span></span>   | <span data-ttu-id="d4905-121">应用使用的 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="d4905-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="d4905-122">**资源组**</span><span class="sxs-lookup"><span data-stu-id="d4905-122">**Resource Group**</span></span> | <span data-ttu-id="d4905-123">应用所属的一组相关资源。</span><span class="sxs-lookup"><span data-stu-id="d4905-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="d4905-124">**托管计划**</span><span class="sxs-lookup"><span data-stu-id="d4905-124">**Hosting Plan**</span></span>   | <span data-ttu-id="d4905-125">Web 应用的定价计划。</span><span class="sxs-lookup"><span data-stu-id="d4905-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="d4905-126">选择 " **服务依赖关系** " 部分中的 " **Azure :::no-loc(SignalR)::: 服务** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-126">Select **Azure :::no-loc(SignalR)::: Service** in the **Service Dependencies** section.</span></span> <span data-ttu-id="d4905-127">选择 **+** 按钮：</span><span class="sxs-lookup"><span data-stu-id="d4905-127">Select the **+** button:</span></span>

   !["依赖关系" 区域显示在 "添加" 下拉列表中选择的 Azure：：： no (SignalR) ：：： Service](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="d4905-129">在 " **Azure :::no-loc(SignalR)::: 服务** " 对话框中，选择 " **创建新的 Azure :::no-loc(SignalR)::: 服务实例** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-129">In the **Azure :::no-loc(SignalR)::: Service** dialog, select **Create a new Azure :::no-loc(SignalR)::: Service instance** .</span></span>

1. <span data-ttu-id="d4905-130">提供 **名称** 、 **资源组** 和 **位置** 。</span><span class="sxs-lookup"><span data-stu-id="d4905-130">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="d4905-131">返回到 " **Azure :::no-loc(SignalR)::: 服务** " 对话框，然后选择 " **添加** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-131">Return to the **Azure :::no-loc(SignalR)::: Service** dialog and select **Add** .</span></span>

<span data-ttu-id="d4905-132">Visual Studio 完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="d4905-132">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="d4905-133">创建包含发布设置的发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="d4905-133">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="d4905-134">使用提供的详细信息创建 *Azure Web 应用* 。</span><span class="sxs-lookup"><span data-stu-id="d4905-134">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="d4905-135">发布应用程序。</span><span class="sxs-lookup"><span data-stu-id="d4905-135">Publishes the app.</span></span>
* <span data-ttu-id="d4905-136">启动加载 web 应用程序的浏览器。</span><span class="sxs-lookup"><span data-stu-id="d4905-136">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="d4905-137">应用的 URL 的格式为 `{APP SERVICE NAME}.azurewebsites.net` 。</span><span class="sxs-lookup"><span data-stu-id="d4905-137">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="d4905-138">例如，名为的应用程序 `:::no-loc(SignalR):::ChatApp` 具有的 URL `https://signalrchatapp.azurewebsites.net` 。</span><span class="sxs-lookup"><span data-stu-id="d4905-138">For example, an app named `:::no-loc(SignalR):::ChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="d4905-139">如果在部署面向预览版 .NET Core 版本的应用时发生 HTTP *502.2 错误的网关* 错误，请参阅 [部署 ASP.NET Core 预览版本 Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) 以解决此问题。</span><span class="sxs-lookup"><span data-stu-id="d4905-139">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="d4905-140">在 Azure App Service 中配置应用</span><span class="sxs-lookup"><span data-stu-id="d4905-140">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="d4905-141">*本部分仅适用于不使用 Azure 服务的应用 :::no-loc(SignalR)::: 。*</span><span class="sxs-lookup"><span data-stu-id="d4905-141">*This section only applies to apps not using the Azure :::no-loc(SignalR)::: Service.*</span></span>
>
> <span data-ttu-id="d4905-142">如果应用使用 Azure :::no-loc(SignalR)::: 服务，应用服务不需要配置应用程序请求路由 (ARR) 相关性和 Web 套接字。</span><span class="sxs-lookup"><span data-stu-id="d4905-142">If the app uses the Azure :::no-loc(SignalR)::: Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="d4905-143">客户端将其 Web 套接字连接到 Azure :::no-loc(SignalR)::: 服务，而不是直接连接到应用程序。</span><span class="sxs-lookup"><span data-stu-id="d4905-143">Clients connect their Web Sockets to the Azure :::no-loc(SignalR)::: Service, not directly to the app.</span></span>

<span data-ttu-id="d4905-144">对于不使用 Azure 服务托管的应用 :::no-loc(SignalR)::: ，请启用：</span><span class="sxs-lookup"><span data-stu-id="d4905-144">For apps hosted without the Azure :::no-loc(SignalR)::: Service, enable:</span></span>

* <span data-ttu-id="d4905-145">[ARR 相关性] (https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- :::no-loc(cookie)::: (ARR :::no-loc(cookie):::) # B0 l) 将来自用户的请求路由回同一应用服务实例。</span><span class="sxs-lookup"><span data-stu-id="d4905-145">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-:::no-loc(cookie):::-(ARR-:::no-loc(cookie):::)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="d4905-146">默认设置为 **"打开** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-146">The default setting is **On** .</span></span>
* <span data-ttu-id="d4905-147">允许 Web 套接字传输正常工作的[Web 套接字](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="d4905-147">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="d4905-148">默认设置为 " **关闭** "。</span><span class="sxs-lookup"><span data-stu-id="d4905-148">The default setting is **Off** .</span></span>

1. <span data-ttu-id="d4905-149">在 Azure 门户中，导航到 **应用服务** 中的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="d4905-149">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="d4905-150">打开 **配置**  >  **常规设置** 。</span><span class="sxs-lookup"><span data-stu-id="d4905-150">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="d4905-151">将 " **Web 套接字** " 设置为 **"开"** 。</span><span class="sxs-lookup"><span data-stu-id="d4905-151">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="d4905-152">验证 **ARR 相关性** 是否设置为 **On** 。</span><span class="sxs-lookup"><span data-stu-id="d4905-152">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="d4905-153">应用服务计划限制</span><span class="sxs-lookup"><span data-stu-id="d4905-153">App Service Plan limits</span></span>

<span data-ttu-id="d4905-154">基于所选的应用服务计划，Web 套接字和其他传输受到限制。</span><span class="sxs-lookup"><span data-stu-id="d4905-154">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="d4905-155">有关详细信息，请参阅 azure [订阅和服务限制、配额和约束](/azure/azure-subscription-service-limits#app-service-limits)一文中的 *azure 云服务限制* 和 *应用服务限制* 部分。</span><span class="sxs-lookup"><span data-stu-id="d4905-155">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4905-156">其他资源</span><span class="sxs-lookup"><span data-stu-id="d4905-156">Additional resources</span></span>

* [<span data-ttu-id="d4905-157">什么是 Azure :::no-loc(SignalR)::: 服务？</span><span class="sxs-lookup"><span data-stu-id="d4905-157">What is Azure :::no-loc(SignalR)::: Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="d4905-158">使用命令行工具将 ASP.NET Core 应用发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="d4905-158">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="d4905-159">在 Azure 上托管和部署 ASP.NET Core 预览应用</span><span class="sxs-lookup"><span data-stu-id="d4905-159">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
