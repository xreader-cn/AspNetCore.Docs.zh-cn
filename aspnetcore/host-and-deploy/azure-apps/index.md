---
title: 将 ASP.NET Core 应用部署到 Azure 应用服务
author: guardrex
description: 本文包含 Azure 主机和部署资源的链接。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: bda4923adb0f9769f883ef64f7902c8650308222
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71924893"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="90bb7-103">将 ASP.NET Core 应用部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="90bb7-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="90bb7-104">[Azure 应用服务](https://azure.microsoft.com/services/app-service/)是一个用于托管 Web 应用（包括 ASP.NET Core）的 [Microsoft 云计算平台服务](https://azure.microsoft.com/)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="90bb7-105">有用资源</span><span class="sxs-lookup"><span data-stu-id="90bb7-105">Useful resources</span></span>

<span data-ttu-id="90bb7-106">Azure 应用文档、教程、示例、操作指南和其他资源由[应用服务文档](/azure/app-service/)提供。</span><span class="sxs-lookup"><span data-stu-id="90bb7-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="90bb7-107">两个有关托管 ASP.NET Core 应用的重要教程为：</span><span class="sxs-lookup"><span data-stu-id="90bb7-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="90bb7-108">在 Azure 中创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="90bb7-109">使用 Visual Studio 创建 ASP.NET Core Web 应用，并将其部署到 Windows 上的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="90bb7-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="90bb7-110">在 Linux 上的应用服务中创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="90bb7-111">使用命令行创建 ASP.NET Core Web 应用，并将其部署到 Linux 上的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="90bb7-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="90bb7-112">有关 Azure 应用服务上可用的 ASP.NET Core 版本，请参阅[应用服务仪表板上的 ASP.NET Core](https://aspnetcoreon.azurewebsites.net/)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="90bb7-113">ASP.NET Core 文档中提供以下文章：</span><span class="sxs-lookup"><span data-stu-id="90bb7-113">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="90bb7-114">了解如何使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="90bb7-114">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="90bb7-115">了解如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用 Git 将它部署到 Azure 应用服务以实现持续部署。</span><span class="sxs-lookup"><span data-stu-id="90bb7-115">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="90bb7-116">创建第一个管道</span><span class="sxs-lookup"><span data-stu-id="90bb7-116">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="90bb7-117">为 ASP.NET Core 应用设置 CI 生成，然后创建针对 Azure 应用服务的持续部署发布。</span><span class="sxs-lookup"><span data-stu-id="90bb7-117">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="90bb7-118">Azure Web 应用沙盒</span><span class="sxs-lookup"><span data-stu-id="90bb7-118">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="90bb7-119">探索 Azure Apps 平台强制实施的 Azure 应用服务运行时执行限制。</span><span class="sxs-lookup"><span data-stu-id="90bb7-119">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="90bb7-120">理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。</span><span class="sxs-lookup"><span data-stu-id="90bb7-120">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="90bb7-121">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="90bb7-121">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="90bb7-122">Platform</span><span class="sxs-lookup"><span data-stu-id="90bb7-122">Platform</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="90bb7-123">64 位 (x64) 和 32 位 (x86) 应用的运行时存在于 Azure 应用服务上。</span><span class="sxs-lookup"><span data-stu-id="90bb7-123">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="90bb7-124">应用服务上提供的 [.NET Core SDK](/dotnet/core/sdk) 为 32 位，但可使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 控制台或通过 Visual Studio 中的发布流程来部署本地构建的 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-124">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="90bb7-125">有关详细信息，请参阅[发布和部署应用](#publish-and-deploy-the-app)部分。</span><span class="sxs-lookup"><span data-stu-id="90bb7-125">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="90bb7-126">对于具有本机依赖项的应用，32 位 (x86) 应用的运行时存在于 Azure 应用服务上。</span><span class="sxs-lookup"><span data-stu-id="90bb7-126">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="90bb7-127">应用服务上提供的 [.NET Core SDK](/dotnet/core/sdk) 为 32 位。</span><span class="sxs-lookup"><span data-stu-id="90bb7-127">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="90bb7-128">要详细了解 .NET Core 框架组件和分发方法（例如有关 .NET Core 运行时和 .NET Core SDK 的信息），请参阅[关于 .NET Core：撰写](/dotnet/core/about#composition)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-128">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="90bb7-129">package</span><span class="sxs-lookup"><span data-stu-id="90bb7-129">Packages</span></span>

<span data-ttu-id="90bb7-130">包含以下 NuGet 包，以便为部署到 Azure 应用服务的应用提供自动日志记录功能：</span><span class="sxs-lookup"><span data-stu-id="90bb7-130">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="90bb7-131">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) 使用 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) 提供 ASP.NET Core 与 Azure 应用服务的启动集成。</span><span class="sxs-lookup"><span data-stu-id="90bb7-131">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="90bb7-132">添加的日志记录功能由 `Microsoft.AspNetCore.AzureAppServicesIntegration` 包提供。</span><span class="sxs-lookup"><span data-stu-id="90bb7-132">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="90bb7-133">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) 执行 [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics)，在 `Microsoft.Extensions.Logging.AzureAppServices` 包中添加 Azure 应用服务诊断日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="90bb7-133">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="90bb7-134">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) 提供记录器实现，支持 Azure 应用服务诊断日志和日志流式处理功能。</span><span class="sxs-lookup"><span data-stu-id="90bb7-134">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="90bb7-135">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不提供前述包。</span><span class="sxs-lookup"><span data-stu-id="90bb7-135">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="90bb7-136">面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包的应用必须显式引用应用的项目文件中的各个包。</span><span class="sxs-lookup"><span data-stu-id="90bb7-136">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="90bb7-137">使用 Azure 门户重写应用配置</span><span class="sxs-lookup"><span data-stu-id="90bb7-137">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="90bb7-138">Azure 门户中的应用设置允许为应用设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="90bb7-138">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="90bb7-139">可以通过[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)来使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="90bb7-139">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="90bb7-140">在 Azure 门户中创建或修改应用设置并选择“保存”按钮时，Azure 应用将重启  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-140">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="90bb7-141">服务重启后，应用即可使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="90bb7-141">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90bb7-142">当应用使用[通用主机](xref:fundamentals/host/generic-host)时，环境变量在默认情况下不会加载到应用的配置，且配置提供程序必须由开发人员添加。</span><span class="sxs-lookup"><span data-stu-id="90bb7-142">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables aren't loaded into an app's configuration by default and the configuration provider must be added by the developer.</span></span> <span data-ttu-id="90bb7-143">在添加配置提供程序时，开发人员将确定环境变量前缀。</span><span class="sxs-lookup"><span data-stu-id="90bb7-143">The developer determines the environment variable prefix when the configuration provider is added.</span></span> <span data-ttu-id="90bb7-144">有关详细信息，请参阅 <xref:fundamentals/host/generic-host> 和[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-144">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90bb7-145">当应用使用 [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 生成主机时，配置主机的环境变量会使用 `ASPNETCORE_`。</span><span class="sxs-lookup"><span data-stu-id="90bb7-145">When an app builds the host using [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), environment variables that configure the host use the `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="90bb7-146">有关详细信息，请参阅 <xref:fundamentals/host/web-host> 和[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-146">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="90bb7-147">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="90bb7-147">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="90bb7-148">托管在[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时配置转接头中间件的 [IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="90bb7-148">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="90bb7-149">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="90bb7-149">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="90bb7-150">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-150">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="90bb7-151">监视和日志记录</span><span class="sxs-lookup"><span data-stu-id="90bb7-151">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90bb7-152">部署到应用服务的 ASP.NET Core 应用会自动接收“ASP.NET Core 记录集成”这一应用服务扩展  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-152">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="90bb7-153">借助该扩展，可记录 Azure 应用服务上针对 ASP.NET Core 应用的集成。</span><span class="sxs-lookup"><span data-stu-id="90bb7-153">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90bb7-154">部署到应用服务的 ASP.NET Core 应用自动接收应用服务扩展“ASP.NET Core 日志记录扩展”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-154">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="90bb7-155">借助该扩展，可记录 Azure 应用服务上针对 ASP.NET Core 应用的集成。</span><span class="sxs-lookup"><span data-stu-id="90bb7-155">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="90bb7-156">有关监视、日志记录和故障排除的信息，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="90bb7-156">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="90bb7-157">监视 Azure 应用服务中的应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-157">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="90bb7-158">了解如何查看应用和应用服务计划的配额和指标。</span><span class="sxs-lookup"><span data-stu-id="90bb7-158">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="90bb7-159">在 Azure 应用服务中为应用启用诊断日志记录</span><span class="sxs-lookup"><span data-stu-id="90bb7-159">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="90bb7-160">了解如何启用和访问 HTTP 状态代码、失败请求和 Web 服务器活动的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="90bb7-160">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="90bb7-161">了解在 ASP.NET Core 应用中处理错误的常见方法。</span><span class="sxs-lookup"><span data-stu-id="90bb7-161">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="90bb7-162">了解如何使用 ASP.NET Core 应用诊断 Azure 应用服务部署问题。</span><span class="sxs-lookup"><span data-stu-id="90bb7-162">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="90bb7-163">使用故障排除建议查看 Azure 应用服务/IIS 托管的应用的常见部署配置错误。</span><span class="sxs-lookup"><span data-stu-id="90bb7-163">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="90bb7-164">数据保护密钥环和部署槽位</span><span class="sxs-lookup"><span data-stu-id="90bb7-164">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="90bb7-165">[数据保护密钥](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)保存在 %HOME%\ASP.NET\DataProtection-Keys 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-165">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="90bb7-166">此文件夹由网络存储提供支持，并跨托管应用的所有计算机同步。</span><span class="sxs-lookup"><span data-stu-id="90bb7-166">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="90bb7-167">密钥不是静态保护的。</span><span class="sxs-lookup"><span data-stu-id="90bb7-167">Keys aren't protected at rest.</span></span> <span data-ttu-id="90bb7-168">此文件夹向单个部署槽位中应用的所有实例提供密钥环。</span><span class="sxs-lookup"><span data-stu-id="90bb7-168">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="90bb7-169">各部署槽位（例如过渡槽和生成槽）不共享密钥环。</span><span class="sxs-lookup"><span data-stu-id="90bb7-169">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="90bb7-170">在部署槽位之间交换时，使用数据保护的任意系统都无法使用之前槽位中的密钥环来解密存储的数据。</span><span class="sxs-lookup"><span data-stu-id="90bb7-170">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="90bb7-171">ASP.NET Cookie 中间件使用数据保护来保护其 cookie。</span><span class="sxs-lookup"><span data-stu-id="90bb7-171">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="90bb7-172">这导致用户注销使用标准 ASP.NET Cookie 中间件的应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-172">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="90bb7-173">对于独立于槽位的密钥环解决方案，请使用外部密钥环提供程序，例如：</span><span class="sxs-lookup"><span data-stu-id="90bb7-173">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="90bb7-174">Azure Blob 存储</span><span class="sxs-lookup"><span data-stu-id="90bb7-174">Azure Blob Storage</span></span>
* <span data-ttu-id="90bb7-175">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="90bb7-175">Azure Key Vault</span></span>
* <span data-ttu-id="90bb7-176">SQL 存储</span><span class="sxs-lookup"><span data-stu-id="90bb7-176">SQL store</span></span>
* <span data-ttu-id="90bb7-177">Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="90bb7-177">Redis cache</span></span>

<span data-ttu-id="90bb7-178">有关详细信息，请参阅 <xref:security/data-protection/implementation/key-storage-providers>。</span><span class="sxs-lookup"><span data-stu-id="90bb7-178">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>
<!-- revert this after 3.0 supported
## Deploy ASP.NET Core preview release to Azure App Service

Use one of the following approaches if the app relies on a preview release of .NET Core:

* [Install the preview site extension](#install-the-preview-site-extension).
* [Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).
* [Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).
-->
## <a name="deploy-aspnet-core-30-to-azure-app-service"></a><span data-ttu-id="90bb7-179">将 ASP.NET Core 3.0 部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="90bb7-179">Deploy ASP.NET Core 3.0 to Azure App Service</span></span>

<span data-ttu-id="90bb7-180">我们希望尽快在 Azure 应用服务中提供 ASP.NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="90bb7-180">We hope to have ASP.NET Core 3.0 available on Azure App Service soon.</span></span>

<span data-ttu-id="90bb7-181">如果应用依赖于 .NET Core 3.0，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="90bb7-181">Use one of the following approaches if the app relies on .NET Core 3.0:</span></span>

* <span data-ttu-id="90bb7-182">[安装预览站点扩展](#install-the-preview-site-extension)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-182">[Install the preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="90bb7-183">[部署独立式预览版应用](#deploy-a-self-contained-preview-app)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-183">[Deploy a self-contained preview app](#deploy-a-self-contained-preview-app).</span></span>
* <span data-ttu-id="90bb7-184">[对用于容器的 Web 应用使用 Docker](#use-docker-with-web-apps-for-containers)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-184">[Use Docker with Web Apps for containers](#use-docker-with-web-apps-for-containers).</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="90bb7-185">安装预览站点扩展</span><span class="sxs-lookup"><span data-stu-id="90bb7-185">Install the preview site extension</span></span>

<span data-ttu-id="90bb7-186">如果无法使用预览版站点扩展，请提出 [aspnet/AspNetCore 问题](https://github.com/aspnet/AspNetCore/issues)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-186">If a problem occurs using the preview site extension, open an [aspnet/AspNetCore issue](https://github.com/aspnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="90bb7-187">从 Azure 门户导航到“应用服务”。</span><span class="sxs-lookup"><span data-stu-id="90bb7-187">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="90bb7-188">选择 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-188">Select the web app.</span></span>
1. <span data-ttu-id="90bb7-189">在搜索框中键入“ex”以筛选“扩展”，或向下滚动管理工具列表。</span><span class="sxs-lookup"><span data-stu-id="90bb7-189">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="90bb7-190">选择“扩展”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-190">Select **Extensions**.</span></span>
1. <span data-ttu-id="90bb7-191">选择“添加”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-191">Select **Add**.</span></span>
1. <span data-ttu-id="90bb7-192">从列表选择“ASP.NET Core {X.Y} ({x64|x86}) 运行时”  扩展，其中 `{X.Y}` 是 ASP.NET Core 预览版本，并且 `{x64|x86}` 指定平台。</span><span class="sxs-lookup"><span data-stu-id="90bb7-192">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="90bb7-193">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="90bb7-193">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="90bb7-194">选择“确定”安装扩展  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-194">Select **OK** to install the extension.</span></span>

<span data-ttu-id="90bb7-195">操作完成时，即表示已安装最新的 .NET Core 预览版。</span><span class="sxs-lookup"><span data-stu-id="90bb7-195">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="90bb7-196">验证安装：</span><span class="sxs-lookup"><span data-stu-id="90bb7-196">Verify the installation:</span></span>

1. <span data-ttu-id="90bb7-197">选择“高级工具”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-197">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="90bb7-198">选择“高级工具”  中的“Go”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-198">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="90bb7-199">选择“调试控制台”   > “PowerShell”  菜单项。</span><span class="sxs-lookup"><span data-stu-id="90bb7-199">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="90bb7-200">从 PowerShell 命令提示符处执行以下命令。</span><span class="sxs-lookup"><span data-stu-id="90bb7-200">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="90bb7-201">在以下命令中，将 ASP.NET Core 运行时版本替换为 `{X.Y}`，并将平台替换为 `{PLATFORM}`：</span><span class="sxs-lookup"><span data-stu-id="90bb7-201">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="90bb7-202">如果安装 x64 预览版运行时，该命令将返回`True`。</span><span class="sxs-lookup"><span data-stu-id="90bb7-202">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="90bb7-203">对于在 A 系列计算机上或更高级托管层上托管的应用，可在 Azure 门户中的应用设置中设置应用服务应用的平台体系结构 (x86/x64)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-203">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute or better hosting tier.</span></span> <span data-ttu-id="90bb7-204">如果应用在进程内模式下运行并且平台体系结构配置为 64 位 (x64)，则 ASP.NET Core 模块会使用 64 位预览版运行时（如存在）。</span><span class="sxs-lookup"><span data-stu-id="90bb7-204">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="90bb7-205">安装“ASP.NET Core {X.Y} (x64) 运行时”  扩展。</span><span class="sxs-lookup"><span data-stu-id="90bb7-205">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension.</span></span>
>
> <span data-ttu-id="90bb7-206">安装 x64 预览版运行时后，在 Kudu PowerShell 命令窗口中运行以下命令以验证该安装。</span><span class="sxs-lookup"><span data-stu-id="90bb7-206">After installing the x64 preview runtime, run the following command in the Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="90bb7-207">在以下命令中，将 ASP.NET Core 运行时版本替换为 `{X.Y}` ：</span><span class="sxs-lookup"><span data-stu-id="90bb7-207">Substitute the ASP.NET Core runtime version for `{X.Y}` in the command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="90bb7-208">如果安装 x64 预览版运行时，该命令将返回`True`。</span><span class="sxs-lookup"><span data-stu-id="90bb7-208">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="90bb7-209">ASP.NET Core 扩展  可为 Azure 应用服务上的 ASP.NET Core 启用附加功能，例如启用 Azure 日志记录。</span><span class="sxs-lookup"><span data-stu-id="90bb7-209">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="90bb7-210">从 Visual Studio 进行部署时，将自动安装该扩展。</span><span class="sxs-lookup"><span data-stu-id="90bb7-210">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="90bb7-211">如果未安装该扩展，请为应用安装它。</span><span class="sxs-lookup"><span data-stu-id="90bb7-211">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="90bb7-212">**通过 ARM 模板使用预览站点扩展**</span><span class="sxs-lookup"><span data-stu-id="90bb7-212">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="90bb7-213">如果使用 ARM 模板创建和部署应用，则可使用 `siteextensions` 资源类型将站点扩展添加到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-213">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="90bb7-214">例如:</span><span class="sxs-lookup"><span data-stu-id="90bb7-214">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="90bb7-215">部署独立式预览版应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-215">Deploy a self-contained preview app</span></span>

<span data-ttu-id="90bb7-216">针对预览运行时的[自包含部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)在部署中承载预览运行时。</span><span class="sxs-lookup"><span data-stu-id="90bb7-216">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="90bb7-217">部署自包含应用时：</span><span class="sxs-lookup"><span data-stu-id="90bb7-217">When deploying a self-contained app:</span></span>

* <span data-ttu-id="90bb7-218">Azure 应用服务中的站点不需要[预览站点扩展名](#install-the-preview-site-extension)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-218">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="90bb7-219">必须使用不同于发布[依赖框架部署 (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd) 的方法发布应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-219">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="90bb7-220">按照[部署独立式应用](#deploy-the-app-self-contained)部分中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="90bb7-220">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="90bb7-221">对用于容器的 Web 应用使用 Docker</span><span class="sxs-lookup"><span data-stu-id="90bb7-221">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="90bb7-222">[Docker 中心](https://hub.docker.com/r/microsoft/aspnetcore/)包含最新的预览 Docker 映像。</span><span class="sxs-lookup"><span data-stu-id="90bb7-222">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="90bb7-223">这些映像可以用作基础映像。</span><span class="sxs-lookup"><span data-stu-id="90bb7-223">The images can be used as a base image.</span></span> <span data-ttu-id="90bb7-224">按常规方法使用映像并部署到用于容器的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-224">Use the image and deploy to Web Apps for Containers normally.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="90bb7-225">发布和部署应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-225">Publish and deploy the app</span></span>

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="90bb7-226">部署依赖框架的应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-226">Deploy the app framework-dependent</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="90bb7-227">对于 64 位[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="90bb7-227">For a 64-bit [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

* <span data-ttu-id="90bb7-228">使用 64 位 .NET Core SDK 部署 64 位的应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-228">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="90bb7-229">在应用服务的“配置” > “常规”l“设置”中将“平台”设置为“64 位”     。</span><span class="sxs-lookup"><span data-stu-id="90bb7-229">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="90bb7-230">应用必须使用基本服务计划或更高级别的服务计划才能选择平台位数。</span><span class="sxs-lookup"><span data-stu-id="90bb7-230">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="90bb7-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90bb7-231">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="90bb7-232">从 Visual Studio 工具栏中选择“构建” > “发布 {应用程序名称}”，或者在解决方案资源管理器中右键单击项目，并选择“发布”     。</span><span class="sxs-lookup"><span data-stu-id="90bb7-232">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="90bb7-233">在“选择发布目标”对话框中，确认已选中“应用服务”   。</span><span class="sxs-lookup"><span data-stu-id="90bb7-233">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="90bb7-234">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-234">Select **Advanced**.</span></span> <span data-ttu-id="90bb7-235">随即会打开“发布”对话框  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-235">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="90bb7-236">在“发布”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="90bb7-236">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="90bb7-237">确认已选中“发布”配置  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-237">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="90bb7-238">打开“部署模式”下拉列表，然后选择“依赖框架”   。</span><span class="sxs-lookup"><span data-stu-id="90bb7-238">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="90bb7-239">选择“可移植”作为目标运行时   。</span><span class="sxs-lookup"><span data-stu-id="90bb7-239">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="90bb7-240">如果需要在部署时删除其他文件，请打开“文件发布选项”，然后选中复选框以删除目标位置的其他文件  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-240">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="90bb7-241">选择“保存”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-241">Select **Save**.</span></span>
1. <span data-ttu-id="90bb7-242">按照发布向导的其余提示创建新站点或更新现有站点。</span><span class="sxs-lookup"><span data-stu-id="90bb7-242">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="90bb7-243">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="90bb7-243">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="90bb7-244">在项目文件中，不要指定[运行时标识符 (RID)](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-244">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="90bb7-245">在命令 shell 中，使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令在发布配置中发布应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-245">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="90bb7-246">在下例中，应用发布为依赖框架的应用：</span><span class="sxs-lookup"><span data-stu-id="90bb7-246">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="90bb7-247">将 bin/Release/{TARGET FRAMEWORK}/publish 目录的内容移动到应用服务中的站点  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-247">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="90bb7-248">如果将 publish 文件夹内容从本地硬盘或网络共享直接拖动到 [Kudu](https://github.com/projectkudu/kudu/wiki) 控制台中的应用服务，则请将文件拖动到 Kudu 控制台的 `D:\home\site\wwwroot` 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-248">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="90bb7-249">部署自包含应用</span><span class="sxs-lookup"><span data-stu-id="90bb7-249">Deploy the app self-contained</span></span>

<span data-ttu-id="90bb7-250">对[独立式部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)使用 Visual Studio 或命令行界面 (CLI) 工具。</span><span class="sxs-lookup"><span data-stu-id="90bb7-250">Use Visual Studio or the command-line interface (CLI) tools for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="90bb7-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90bb7-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="90bb7-252">从 Visual Studio 工具栏中选择“构建” > “发布 {应用程序名称}”，或者在解决方案资源管理器中右键单击项目，并选择“发布”     。</span><span class="sxs-lookup"><span data-stu-id="90bb7-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="90bb7-253">在“选择发布目标”对话框中，确认已选中“应用服务”   。</span><span class="sxs-lookup"><span data-stu-id="90bb7-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="90bb7-254">选择“高级”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-254">Select **Advanced**.</span></span> <span data-ttu-id="90bb7-255">随即会打开“发布”对话框  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="90bb7-256">在“发布”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="90bb7-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="90bb7-257">确认已选中“发布”配置  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="90bb7-258">打开“部署模式”下拉列表，然后选择“自包含”   。</span><span class="sxs-lookup"><span data-stu-id="90bb7-258">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="90bb7-259">从“目标运行时”下拉列表中选择目标运行时  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-259">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="90bb7-260">默认值为 `win-x86`。</span><span class="sxs-lookup"><span data-stu-id="90bb7-260">The default is `win-x86`.</span></span>
   * <span data-ttu-id="90bb7-261">如果需要在部署时删除其他文件，请打开“文件发布选项”，然后选中复选框以删除目标位置的其他文件  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-261">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="90bb7-262">选择“保存”  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-262">Select **Save**.</span></span>
1. <span data-ttu-id="90bb7-263">按照发布向导的其余提示创建新站点或更新现有站点。</span><span class="sxs-lookup"><span data-stu-id="90bb7-263">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="90bb7-264">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="90bb7-264">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="90bb7-265">在项目文件中，指定一个或多个[运行时标识符 (RID)](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-265">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="90bb7-266">对单个 RID 使用 `<RuntimeIdentifier>`（单数），或使用 `<RuntimeIdentifiers>`（复数）提供以分号分隔的 RID 列表。</span><span class="sxs-lookup"><span data-stu-id="90bb7-266">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="90bb7-267">在以下示例中，指定 `win-x86` RID：</span><span class="sxs-lookup"><span data-stu-id="90bb7-267">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="90bb7-268">在命令 shell 中，使用 [dotnet publish ](/dotnet/core/tools/dotnet-publish) 命令在主机运行时的发布配置中发布应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-268">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="90bb7-269">在以下示例中，为 `win-x86` RID 发布应用。</span><span class="sxs-lookup"><span data-stu-id="90bb7-269">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="90bb7-270">提供给 `--runtime` 选项的 RID 必须在项目文件的 `<RuntimeIdentifier>`（或 `<RuntimeIdentifiers>`）属性中提供。</span><span class="sxs-lookup"><span data-stu-id="90bb7-270">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="90bb7-271">将 bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish 目录的内容移动到应用服务中的站点  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-271">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="90bb7-272">如果将 publish 文件夹内容从本地硬盘或网络共享直接拖动到 Kudu 控制台中的应用服务，则请将文件拖动到 Kudu 控制台的 `D:\home\site\wwwroot` 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-272">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="90bb7-273">协议设置 (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="90bb7-273">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="90bb7-274">借助安全的协议绑定，可在通过 HTTPS 响应请求时指定要使用的证书。</span><span class="sxs-lookup"><span data-stu-id="90bb7-274">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="90bb7-275">若要绑定，需要一个为特定主机名颁发的有效专用证书 ( *.pfx*)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-275">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="90bb7-276">有关详细信息，请参阅[教程：将现有自定义 SSL 证书绑定到 Azure 应用服务](/azure/app-service/app-service-web-tutorial-custom-ssl)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-276">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="90bb7-277">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="90bb7-277">Transform web.config</span></span>

<span data-ttu-id="90bb7-278">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>  。</span><span class="sxs-lookup"><span data-stu-id="90bb7-278">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90bb7-279">其他资源</span><span class="sxs-lookup"><span data-stu-id="90bb7-279">Additional resources</span></span>

* [<span data-ttu-id="90bb7-280">应用服务概述</span><span class="sxs-lookup"><span data-stu-id="90bb7-280">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="90bb7-281">Azure 应用服务：托管 .NET 应用的最佳位置（55 分钟概览视频）</span><span class="sxs-lookup"><span data-stu-id="90bb7-281">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="90bb7-282">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="90bb7-282">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="90bb7-283">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="90bb7-283">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="90bb7-284">Windows Server 上的 Azure 应用服务使用 [Internet Information Services (IIS)](https://www.iis.net/)。</span><span class="sxs-lookup"><span data-stu-id="90bb7-284">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="90bb7-285">以下是基础 IIS 技术的相关主题：</span><span class="sxs-lookup"><span data-stu-id="90bb7-285">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="90bb7-286">Windows Server - 当前和以前版本的 IT 管理员内容</span><span class="sxs-lookup"><span data-stu-id="90bb7-286">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
