---
title: 将 ASP.NET Core 应用部署到 Azure 应用服务
author: bradygaster
description: 本文包含 Azure 主机和部署资源的链接。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644610"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="c3cd1-103">将 ASP.NET Core 应用部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="c3cd1-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="c3cd1-104">[Azure 应用服务](https://azure.microsoft.com/services/app-service/)是一个用于托管 Web 应用（包括 ASP.NET Core）的 [Microsoft 云计算平台服务](https://azure.microsoft.com/)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="c3cd1-105">有用资源</span><span class="sxs-lookup"><span data-stu-id="c3cd1-105">Useful resources</span></span>

<span data-ttu-id="c3cd1-106">Azure 应用文档、教程、示例、操作指南和其他资源由[应用服务文档](/azure/app-service/)提供。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="c3cd1-107">两个有关托管 ASP.NET Core 应用的重要教程为：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="c3cd1-108">在 Azure 中创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="c3cd1-109">使用 Visual Studio 创建 ASP.NET Core Web 应用，并将其部署到 Windows 上的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="c3cd1-110">在 Linux 上的应用服务中创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="c3cd1-111">使用命令行创建 ASP.NET Core Web 应用，并将其部署到 Linux 上的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="c3cd1-112">有关 Azure 应用服务上可用的 ASP.NET Core 版本，请参阅[应用服务仪表板上的 ASP.NET Core](https://aspnetcoreon.azurewebsites.net/)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="c3cd1-113">订阅[应用服务公告](https://github.com/Azure/app-service-announcements/)存储库并监视问题。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="c3cd1-114">应用服务团队会定期发布应用服务中的公告和方案。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="c3cd1-115">ASP.NET Core 文档中提供以下文章：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="c3cd1-116">了解如何使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="c3cd1-117">了解如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用 Git 将它部署到 Azure 应用服务以实现持续部署。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="c3cd1-118">创建第一个管道</span><span class="sxs-lookup"><span data-stu-id="c3cd1-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="c3cd1-119">为 ASP.NET Core 应用设置 CI 生成，然后创建针对 Azure 应用服务的持续部署发布。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="c3cd1-120">Azure Web 应用沙盒</span><span class="sxs-lookup"><span data-stu-id="c3cd1-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="c3cd1-121">探索 Azure Apps 平台强制实施的 Azure 应用服务运行时执行限制。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="c3cd1-122">理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="c3cd1-123">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="c3cd1-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="c3cd1-124">Platform</span><span class="sxs-lookup"><span data-stu-id="c3cd1-124">Platform</span></span>

<span data-ttu-id="c3cd1-125">对于在 A 系列计算（基本）上或更高级托管层上托管的应用，可在 Azure 门户中的应用设置中设置应用服务应用的平台体系结构 (x86/x64)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="c3cd1-126">请确认应用的发布设置（例如 Visual Studio 中的[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)）与 Azure 门户中应用的服务配置设置匹配。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c3cd1-127">64 位 (x64) 和 32 位 (x86) 应用的运行时存在于 Azure 应用服务上。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="c3cd1-128">应用服务上提供的 [.NET Core SDK](/dotnet/core/sdk) 为 32 位，但可使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 控制台或通过 Visual Studio 中的发布流程来部署本地构建的 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="c3cd1-129">有关详细信息，请参阅[发布和部署应用](#publish-and-deploy-the-app)部分。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c3cd1-130">对于具有本机依赖项的应用，32 位 (x86) 应用的运行时存在于 Azure 应用服务上。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="c3cd1-131">应用服务上提供的 [.NET Core SDK](/dotnet/core/sdk) 为 32 位。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="c3cd1-132">要详细了解 .NET Core 框架组件和分发方法（例如有关 .NET Core 运行时和 .NET Core SDK 的信息），请参阅[关于 .NET Core：撰写](/dotnet/core/about#composition)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="c3cd1-133">package</span><span class="sxs-lookup"><span data-stu-id="c3cd1-133">Packages</span></span>

<span data-ttu-id="c3cd1-134">包含以下 NuGet 包，以便为部署到 Azure 应用服务的应用提供自动日志记录功能：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="c3cd1-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) 使用 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) 提供 ASP.NET Core 与 Azure 应用服务的启动集成。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="c3cd1-136">添加的日志记录功能由 `Microsoft.AspNetCore.AzureAppServicesIntegration` 包提供。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="c3cd1-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) 执行 [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics)，在 `Microsoft.Extensions.Logging.AzureAppServices` 包中添加 Azure 应用服务诊断日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="c3cd1-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) 提供记录器实现，支持 Azure 应用服务诊断日志和日志流式处理功能。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="c3cd1-139">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不提供前述包。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="c3cd1-140">面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包的应用必须显式引用应用的项目文件中的各个包。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="c3cd1-141">使用 Azure 门户重写应用配置</span><span class="sxs-lookup"><span data-stu-id="c3cd1-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="c3cd1-142">Azure 门户中的应用设置允许为应用设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="c3cd1-143">可以通过[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)来使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="c3cd1-144">在 Azure 门户中创建或修改应用设置并选择“保存”按钮时，Azure 应用将重启  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="c3cd1-145">服务重启后，应用即可使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3cd1-146">当应用使用[一般主机](xref:fundamentals/host/generic-host)时，在调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 来构建主机时，环境变量将加载到应用程序的配置中。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="c3cd1-147">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3cd1-148">当应用使用 [Web 主机](xref:fundamentals/host/web-host)时，在调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 来构建主机时，环境变量将加载到应用程序的配置中。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="c3cd1-149">有关详细信息，请参阅 <xref:fundamentals/host/web-host> 和[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="c3cd1-150">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="c3cd1-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="c3cd1-151">托管在[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时配置转接头中间件的 [IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="c3cd1-152">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="c3cd1-153">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="c3cd1-154">监视和日志记录</span><span class="sxs-lookup"><span data-stu-id="c3cd1-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c3cd1-155">部署到应用服务的 ASP.NET Core 应用会自动接收“ASP.NET Core 记录集成”这一应用服务扩展  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="c3cd1-156">借助该扩展，可记录 Azure 应用服务上针对 ASP.NET Core 应用的集成。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c3cd1-157">部署到应用服务的 ASP.NET Core 应用自动接收应用服务扩展“ASP.NET Core 日志记录扩展”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="c3cd1-158">借助该扩展，可记录 Azure 应用服务上针对 ASP.NET Core 应用的集成。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="c3cd1-159">有关监视、日志记录和故障排除的信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="c3cd1-160">监视 Azure 应用服务中的应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="c3cd1-161">了解如何查看应用和应用服务计划的配额和指标。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="c3cd1-162">在 Azure 应用服务中为应用启用诊断日志记录</span><span class="sxs-lookup"><span data-stu-id="c3cd1-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="c3cd1-163">了解如何启用和访问 HTTP 状态代码、失败请求和 Web 服务器活动的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="c3cd1-164">了解在 ASP.NET Core 应用中处理错误的常见方法。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="c3cd1-165">了解如何使用 ASP.NET Core 应用诊断 Azure 应用服务部署问题。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="c3cd1-166">使用故障排除建议查看 Azure 应用服务/IIS 托管的应用的常见部署配置错误。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="c3cd1-167">数据保护密钥环和部署槽位</span><span class="sxs-lookup"><span data-stu-id="c3cd1-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="c3cd1-168">[数据保护密钥](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)保存在 %HOME%\ASP.NET\DataProtection-Keys 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="c3cd1-169">此文件夹由网络存储提供支持，并跨托管应用的所有计算机同步。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="c3cd1-170">密钥不是静态保护的。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="c3cd1-171">此文件夹向单个部署槽位中应用的所有实例提供密钥环。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="c3cd1-172">各部署槽位（例如过渡槽和生成槽）不共享密钥环。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="c3cd1-173">在部署槽位之间交换时，使用数据保护的任意系统都无法使用之前槽位中的密钥环来解密存储的数据。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="c3cd1-174">ASP.NET Cookie 中间件使用数据保护来保护其 cookie。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="c3cd1-175">这导致用户注销使用标准 ASP.NET Cookie 中间件的应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="c3cd1-176">对于独立于槽位的密钥环解决方案，请使用外部密钥环提供程序，例如：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="c3cd1-177">Azure Blob 存储</span><span class="sxs-lookup"><span data-stu-id="c3cd1-177">Azure Blob Storage</span></span>
* <span data-ttu-id="c3cd1-178">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c3cd1-178">Azure Key Vault</span></span>
* <span data-ttu-id="c3cd1-179">SQL 存储</span><span class="sxs-lookup"><span data-stu-id="c3cd1-179">SQL store</span></span>
* <span data-ttu-id="c3cd1-180">Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="c3cd1-180">Redis cache</span></span>

<span data-ttu-id="c3cd1-181">有关详细信息，请参阅 <xref:security/data-protection/implementation/key-storage-providers>。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="c3cd1-182">部署使用 .NET Core 预览的 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="c3cd1-183">若要部署使用 .NET Core 预览版本的应用，请参阅以下资源。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="c3cd1-184">当运行时可用，但 SDK 尚未安装在 Azure 应用服务上时，也可以使用这些方法。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="c3cd1-185">使用 Azure Pipelines 指定 .NET Core SDK 版本</span><span class="sxs-lookup"><span data-stu-id="c3cd1-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="c3cd1-186">部署独立式预览版应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="c3cd1-187">对用于容器的 Web 应用使用 Docker</span><span class="sxs-lookup"><span data-stu-id="c3cd1-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="c3cd1-188">安装预览站点扩展</span><span class="sxs-lookup"><span data-stu-id="c3cd1-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="c3cd1-189">有关 Azure 应用服务上可用的 ASP.NET Core 版本，请参阅[应用服务仪表板上的 ASP.NET Core](https://aspnetcoreon.azurewebsites.net/)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="c3cd1-190">使用 Azure Pipelines 指定 .NET Core SDK 版本</span><span class="sxs-lookup"><span data-stu-id="c3cd1-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="c3cd1-191">使用 [Azure 应用服务 CI/CD 方案](/azure/app-service/deploy-continuous-deployment)设置与 Azure DevOps 的持续集成版本。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="c3cd1-192">创建 Azure DevOps 版本后，配置版本以使用特定的 SDK 版本（可选）。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="c3cd1-193">指定 .NET Core SDK 版本</span><span class="sxs-lookup"><span data-stu-id="c3cd1-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="c3cd1-194">使用应用服务部署中心创建 Azure DevOps 版本时，默认版本管道包含 `Restore`、`Build`、`Test` 和 `Publish` 的步骤。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="c3cd1-195">若要指定 SDK 版本，请在“代理作业”列表中选择“添加 (+)”  按钮以添加新步骤。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="c3cd1-196">在搜索栏中搜索“.NET Core SDK”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![添加 .NET Core SDK 步骤](index/add-sdk-step.png)

<span data-ttu-id="c3cd1-198">将该步骤移至版本中的第一个位置，使其后面的步骤使用 .NET Core SDK 的指定版本。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="c3cd1-199">指定 .NET Core SDK 版本。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="c3cd1-200">在本示例中，SDK 被设置为 `3.0.100`。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-200">In this example, the SDK is set to `3.0.100`.</span></span>

![完成的 SDK 步骤](index/sdk-step-first-place.png)

<span data-ttu-id="c3cd1-202">若要发布[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)，请在 `Publish` 步骤中配置 SCD 并提供[运行时标识符 (RID)](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![独立发布](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="c3cd1-204">部署独立式预览版应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="c3cd1-205">针对预览运行时的[自包含部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)在部署中承载预览运行时。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="c3cd1-206">部署自包含应用时：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="c3cd1-207">Azure 应用服务中的站点不需要[预览站点扩展名](#install-the-preview-site-extension)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="c3cd1-208">必须使用不同于发布[依赖框架部署 (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd) 的方法发布应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="c3cd1-209">按照[部署独立式应用](#deploy-the-app-self-contained)部分中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="c3cd1-210">对用于容器的 Web 应用使用 Docker</span><span class="sxs-lookup"><span data-stu-id="c3cd1-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="c3cd1-211">[Docker 中心](https://hub.docker.com/r/microsoft/aspnetcore/)包含最新的预览 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="c3cd1-212">这些映像可以用作基础映像。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-212">The images can be used as a base image.</span></span> <span data-ttu-id="c3cd1-213">按常规方法使用映像并部署到用于容器的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="c3cd1-214">安装预览站点扩展</span><span class="sxs-lookup"><span data-stu-id="c3cd1-214">Install the preview site extension</span></span>

<span data-ttu-id="c3cd1-215">如果无法使用预览版站点扩展，请提出 [dotnet/AspNetCore 问题](https://github.com/dotnet/AspNetCore/issues)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-215">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="c3cd1-216">从 Azure 门户导航到“应用服务”。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="c3cd1-217">选择 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-217">Select the web app.</span></span>
1. <span data-ttu-id="c3cd1-218">在搜索框中键入“ex”以筛选“扩展”，或向下滚动管理工具列表。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="c3cd1-219">选择“扩展”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="c3cd1-220">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-220">Select **Add**.</span></span>
1. <span data-ttu-id="c3cd1-221">从列表选择“ASP.NET Core {X.Y} ({x64|x86}) 运行时”  扩展，其中 `{X.Y}` 是 ASP.NET Core 预览版本，并且 `{x64|x86}` 指定平台。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="c3cd1-222">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c3cd1-223">选择“确定”安装扩展  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="c3cd1-224">操作完成时，即表示已安装最新的 .NET Core 预览版。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="c3cd1-225">验证安装：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-225">Verify the installation:</span></span>

1. <span data-ttu-id="c3cd1-226">选择“高级工具”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="c3cd1-227">选择“高级工具”  中的“Go”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="c3cd1-228">选择“调试控制台”   > “PowerShell”  菜单项。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="c3cd1-229">从 PowerShell 命令提示符处执行以下命令。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="c3cd1-230">在以下命令中，将 ASP.NET Core 运行时版本替换为 `{X.Y}`，并将平台替换为 `{PLATFORM}`：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="c3cd1-231">如果安装 x64 预览版运行时，该命令将返回`True`。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="c3cd1-232">对于在 A 系列计算（基本）上或更高级托管层上托管的应用，可在 Azure 门户中的应用设置中设置应用服务应用的平台体系结构 (x86/x64)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="c3cd1-233">请确认应用的发布设置（例如 Visual Studio 中的[发布配置文件 (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)）与 Azure 门户中应用的服务配置设置匹配。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="c3cd1-234">如果应用在进程内模式下运行并且平台体系结构配置为 64 位 (x64)，则 ASP.NET Core 模块会使用 64 位预览版运行时（如存在）。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="c3cd1-235">使用 Azure 门户安装“ASP.NET Core {X.Y} (x64) 运行时”  扩展。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="c3cd1-236">安装 x64 预览版运行时后，在 Azure Kudu PowerShell 命令窗口中运行以下命令以验证该安装。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="c3cd1-237">在以下命令中，将 ASP.NET Core 运行时版本替换为 `{X.Y}`：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="c3cd1-238">如果安装 x64 预览版运行时，该命令将返回`True`。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="c3cd1-239">ASP.NET Core 扩展  可为 Azure 应用服务上的 ASP.NET Core 启用附加功能，例如启用 Azure 日志记录。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="c3cd1-240">从 Visual Studio 进行部署时，将自动安装该扩展。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="c3cd1-241">如果未安装该扩展，请为应用安装它。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="c3cd1-242">**通过 ARM 模板使用预览站点扩展**</span><span class="sxs-lookup"><span data-stu-id="c3cd1-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="c3cd1-243">如果使用 ARM 模板创建和部署应用，则可使用 `siteextensions` 资源类型将站点扩展添加到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="c3cd1-244">例如：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="c3cd1-245">发布和部署应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c3cd1-246">对于 64 位部署：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="c3cd1-247">使用 64 位 .NET Core SDK 部署 64 位的应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="c3cd1-248">在应用服务的“配置” > “常规”l“设置”中将“平台”设置为“64 位”     。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="c3cd1-249">应用必须使用基本服务计划或更高级别的服务计划才能选择平台位数。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="c3cd1-250">部署依赖框架的应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3cd1-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3cd1-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c3cd1-252">从 Visual Studio 工具栏中选择“构建” > “发布 {应用程序名称}”，或者在解决方案资源管理器中右键单击项目，并选择“发布”     。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="c3cd1-253">在“选择发布目标”对话框中，确认已选中“应用服务”   。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="c3cd1-254">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-254">Select **Advanced**.</span></span> <span data-ttu-id="c3cd1-255">随即会打开“发布”对话框  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="c3cd1-256">在“发布”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="c3cd1-257">确认已选中“发布”配置  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="c3cd1-258">打开“部署模式”下拉列表，然后选择“依赖框架”   。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="c3cd1-259">选择“可移植”作为目标运行时   。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="c3cd1-260">如果需要在部署时删除其他文件，请打开“文件发布选项”，然后选中复选框以删除目标位置的其他文件  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="c3cd1-261">选择“保存”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-261">Select **Save**.</span></span>
1. <span data-ttu-id="c3cd1-262">按照发布向导的其余提示创建新站点或更新现有站点。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c3cd1-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c3cd1-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="c3cd1-264">在项目文件中，不要指定[运行时标识符 (RID)](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="c3cd1-265">在命令 shell 中，使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令在发布配置中发布应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c3cd1-266">在下例中，应用发布为依赖框架的应用：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="c3cd1-267">将 bin/Release/{TARGET FRAMEWORK}/publish 目录的内容移动到应用服务中的站点  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="c3cd1-268">如果将 publish 文件夹内容从本地硬盘或网络共享直接拖动到 [Kudu](https://github.com/projectkudu/kudu/wiki) 控制台中的应用服务，则请将文件拖动到 Kudu 控制台的 `D:\home\site\wwwroot` 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="c3cd1-269">部署自包含应用</span><span class="sxs-lookup"><span data-stu-id="c3cd1-269">Deploy the app self-contained</span></span>

<span data-ttu-id="c3cd1-270">对[独立式部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 使用 Visual Studio 或 .NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-270">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c3cd1-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c3cd1-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c3cd1-272">从 Visual Studio 工具栏中选择“构建” > “发布 {应用程序名称}”，或者在解决方案资源管理器中右键单击项目，并选择“发布”     。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="c3cd1-273">在“选择发布目标”对话框中，确认已选中“应用服务”   。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="c3cd1-274">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-274">Select **Advanced**.</span></span> <span data-ttu-id="c3cd1-275">随即会打开“发布”对话框  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="c3cd1-276">在“发布”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="c3cd1-277">确认已选中“发布”配置  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="c3cd1-278">打开“部署模式”下拉列表，然后选择“自包含”   。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="c3cd1-279">从“目标运行时”下拉列表中选择目标运行时  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="c3cd1-280">默认值为 `win-x86`。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="c3cd1-281">如果需要在部署时删除其他文件，请打开“文件发布选项”，然后选中复选框以删除目标位置的其他文件  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="c3cd1-282">选择“保存”  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-282">Select **Save**.</span></span>
1. <span data-ttu-id="c3cd1-283">按照发布向导的其余提示创建新站点或更新现有站点。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c3cd1-284">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c3cd1-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="c3cd1-285">在项目文件中，指定一个或多个[运行时标识符 (RID)](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="c3cd1-286">对单个 RID 使用 `<RuntimeIdentifier>`（单数），或使用 `<RuntimeIdentifiers>`（复数）提供以分号分隔的 RID 列表。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="c3cd1-287">在以下示例中，指定 `win-x86` RID：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="c3cd1-288">在命令 shell 中，使用 [dotnet publish ](/dotnet/core/tools/dotnet-publish) 命令在主机运行时的发布配置中发布应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c3cd1-289">在以下示例中，为 `win-x86` RID 发布应用。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="c3cd1-290">提供给 `--runtime` 选项的 RID 必须在项目文件的 `<RuntimeIdentifier>`（或 `<RuntimeIdentifiers>`）属性中提供。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="c3cd1-291">将 bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish 目录的内容移动到应用服务中的站点  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="c3cd1-292">如果将 publish 文件夹内容从本地硬盘或网络共享直接拖动到 Kudu 控制台中的应用服务，则请将文件拖动到 Kudu 控制台的 `D:\home\site\wwwroot` 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="c3cd1-293">协议设置 (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="c3cd1-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="c3cd1-294">借助安全的协议绑定，可在通过 HTTPS 响应请求时指定要使用的证书。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="c3cd1-295">若要绑定，需要一个为特定主机名颁发的有效专用证书 ( *.pfx*)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="c3cd1-296">有关详细信息，请参阅[教程：将现有自定义 SSL 证书绑定到 Azure 应用服务](/azure/app-service/app-service-web-tutorial-custom-ssl)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="c3cd1-297">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="c3cd1-297">Transform web.config</span></span>

<span data-ttu-id="c3cd1-298">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c3cd1-299">其他资源</span><span class="sxs-lookup"><span data-stu-id="c3cd1-299">Additional resources</span></span>

* [<span data-ttu-id="c3cd1-300">应用服务概述</span><span class="sxs-lookup"><span data-stu-id="c3cd1-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="c3cd1-301">Azure 应用服务：托管 .NET 应用的最佳位置（55 分钟概览视频）</span><span class="sxs-lookup"><span data-stu-id="c3cd1-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="c3cd1-302">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="c3cd1-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="c3cd1-303">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="c3cd1-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="c3cd1-304">Windows Server 上的 Azure 应用服务使用 [Internet Information Services (IIS)](https://www.iis.net/)。</span><span class="sxs-lookup"><span data-stu-id="c3cd1-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="c3cd1-305">以下是基础 IIS 技术的相关主题：</span><span class="sxs-lookup"><span data-stu-id="c3cd1-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="c3cd1-306">Windows Server - 当前和以前版本的 IT 管理员内容</span><span class="sxs-lookup"><span data-stu-id="c3cd1-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
