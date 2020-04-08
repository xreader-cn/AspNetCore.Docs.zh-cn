---
title: 托管和部署 ASP.NET Core
author: rick-anderson
description: 了解如何设置托管环境和部署 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/index
ms.openlocfilehash: 464d19bd63e1f0f06bd7d218e7644afde04a5672
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78644148"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="2aed1-103">托管和部署 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2aed1-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2aed1-104">一般而言，向托管环境部署 ASP.NET Core 应用需执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2aed1-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="2aed1-105">将已发布应用部署到托管服务器上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2aed1-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="2aed1-106">设置进程管理器，该管理器在收到请求时启动应用，并在应用发生故障或服务器重启后重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="2aed1-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="2aed1-107">对于反向代理配置，将反向代理设置为将请求转发到应用。</span><span class="sxs-lookup"><span data-stu-id="2aed1-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="2aed1-108">发布到文件夹</span><span class="sxs-lookup"><span data-stu-id="2aed1-108">Publish to a folder</span></span>

<span data-ttu-id="2aed1-109">[dotnet publish](/dotnet/core/tools/dotnet-publish) 命令编译应用代码，并复制在“发布”  文件夹中运行应用所需的文件。</span><span class="sxs-lookup"><span data-stu-id="2aed1-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="2aed1-110">使用 Visual Studio 进行部署时，自动先执行 `dotnet publish` 步骤，再将文件复制到部署目标。</span><span class="sxs-lookup"><span data-stu-id="2aed1-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="2aed1-111">文件夹内容</span><span class="sxs-lookup"><span data-stu-id="2aed1-111">Folder contents</span></span>

<span data-ttu-id="2aed1-112">“发布”  文件夹包含一个或多个应用程序集文件、依赖项以及（可选）.NET 运行时。</span><span class="sxs-lookup"><span data-stu-id="2aed1-112">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="2aed1-113">.NET Core 应用可以发布为“独立式部署”  ，也可以发布为“依赖框架的部署”  。</span><span class="sxs-lookup"><span data-stu-id="2aed1-113">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="2aed1-114">如果应用是独立式，“发布”  文件夹中有包含 .NET 运行时的程序集文件。</span><span class="sxs-lookup"><span data-stu-id="2aed1-114">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="2aed1-115">如果应用依赖于框架，.NET 运行时文件就不包含在内，因为应用包含对服务器上安装的 .NET 版本的引用。</span><span class="sxs-lookup"><span data-stu-id="2aed1-115">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="2aed1-116">默认部署模型是依赖于框架的模型。</span><span class="sxs-lookup"><span data-stu-id="2aed1-116">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="2aed1-117">有关详细信息，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-117">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="2aed1-118">除了“.exe”  和“.dll”  文件以外，ASP.NET Core 应用的“发布”  文件夹通常包含配置文件、静态资产和 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="2aed1-118">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="2aed1-119">有关更多信息，请参见 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-119">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="2aed1-120">设置进程管理器</span><span class="sxs-lookup"><span data-stu-id="2aed1-120">Set up a process manager</span></span>

<span data-ttu-id="2aed1-121">ASP.NET Core 应用是一个控制台应用，在服务器启动时必须启动该应用，并且在出现故障后必须重新启动它。</span><span class="sxs-lookup"><span data-stu-id="2aed1-121">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="2aed1-122">若要自动启动和重新启动，需要使用进程管理器。</span><span class="sxs-lookup"><span data-stu-id="2aed1-122">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="2aed1-123">用于 ASP.NET Core 的最常见进程管理器是：</span><span class="sxs-lookup"><span data-stu-id="2aed1-123">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="2aed1-124">Linux</span><span class="sxs-lookup"><span data-stu-id="2aed1-124">Linux</span></span>
  * [<span data-ttu-id="2aed1-125">Nginx</span><span class="sxs-lookup"><span data-stu-id="2aed1-125">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="2aed1-126">Apache</span><span class="sxs-lookup"><span data-stu-id="2aed1-126">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="2aed1-127">Windows</span><span class="sxs-lookup"><span data-stu-id="2aed1-127">Windows</span></span>
  * [<span data-ttu-id="2aed1-128">IIS</span><span class="sxs-lookup"><span data-stu-id="2aed1-128">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="2aed1-129">Windows 服务</span><span class="sxs-lookup"><span data-stu-id="2aed1-129">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="2aed1-130">设置反向代理</span><span class="sxs-lookup"><span data-stu-id="2aed1-130">Set up a reverse proxy</span></span>

<span data-ttu-id="2aed1-131">如果应用使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache) 或 [IIS](xref:host-and-deploy/iis/index) 可用作反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="2aed1-131">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="2aed1-132">反向代理服务器接收来自 Internet 的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2aed1-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="2aed1-133">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="2aed1-133">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="2aed1-134">有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-134">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2aed1-135">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="2aed1-135">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2aed1-136">对于托管在代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="2aed1-136">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="2aed1-137">如不提供附加配置，应用可能无法访问方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="2aed1-137">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="2aed1-138">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-138">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="2aed1-139">使用 Visual Studio 和 MSBuild 自动执行部署</span><span class="sxs-lookup"><span data-stu-id="2aed1-139">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="2aed1-140">除将输出从 [dotnet publish](/dotnet/core/tools/dotnet-publish) 复制到服务器外，部署通常还需要其他任务。</span><span class="sxs-lookup"><span data-stu-id="2aed1-140">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="2aed1-141">例如，可能需要从“发布”  文件夹获取或排除额外文件。</span><span class="sxs-lookup"><span data-stu-id="2aed1-141">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="2aed1-142">Visual Studio 使用 MSBuild 进行 Web 部署，用户可以自定义 MSBuild 以在部署期间执行其他任务。</span><span class="sxs-lookup"><span data-stu-id="2aed1-142">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="2aed1-143">有关详细信息，请参阅 <xref:host-and-deploy/visual-studio-publish-profiles> 和[使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/) 一书。</span><span class="sxs-lookup"><span data-stu-id="2aed1-143">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="2aed1-144">通过[发布 Web 功能](xref:tutorials/publish-to-azure-webapp-using-vs)或[内置 Git 支持](xref:host-and-deploy/azure-apps/azure-continuous-deployment)，可以将应用从 Visual Studio 直接部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="2aed1-144">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="2aed1-145">Azure DevOps Services 支持[对 Azure 应用服务进行持续部署](/azure/devops/pipelines/targets/webapp)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-145">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="2aed1-146">有关详细信息，请参阅[通过 ASP.NET Core 和 Azure 实现 DevOps](xref:azure/devops/index)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-146">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="2aed1-147">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="2aed1-147">Publish to Azure</span></span>

<span data-ttu-id="2aed1-148">有关如何使用 Visual Studio 将应用发布到 Azure 的说明，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-148">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="2aed1-149">[在 Azure 中创建 ASP.NET Core Web 应用](/azure/app-service/app-service-web-get-started-dotnet)提供了其他示例。</span><span class="sxs-lookup"><span data-stu-id="2aed1-149">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="2aed1-150">在 Windows 中使用 MSDeploy 发布</span><span class="sxs-lookup"><span data-stu-id="2aed1-150">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="2aed1-151">请参阅 <xref:host-and-deploy/visual-studio-publish-profiles>，了解如何使用 Visual Studio 发布配置文件发布应用，其中包括在 Windows 命令提示符处使用 [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 命令。</span><span class="sxs-lookup"><span data-stu-id="2aed1-151">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="2aed1-152">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="2aed1-152">Internet Information Services (IIS)</span></span>

<span data-ttu-id="2aed1-153">要使用 web.config 文件提供的配置部署到 Internet Information Services (IIS)，请参阅  *下的文章*<xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-153">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="2aed1-154">在 Web 场中托管</span><span class="sxs-lookup"><span data-stu-id="2aed1-154">Host in a web farm</span></span>

<span data-ttu-id="2aed1-155">有关如何配置在 Web 场环境中托管 ASP.NET Core 应用（例如，部署应用的多个实例以实现可伸缩性）的信息，请参阅 <xref:host-and-deploy/web-farm>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-155">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="2aed1-156">在 Docker 中托管</span><span class="sxs-lookup"><span data-stu-id="2aed1-156">Host on Docker</span></span>

<span data-ttu-id="2aed1-157">有关更多信息，请参见 <xref:host-and-deploy/docker/index>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-157">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="2aed1-158">执行运行状况检查</span><span class="sxs-lookup"><span data-stu-id="2aed1-158">Perform health checks</span></span>

<span data-ttu-id="2aed1-159">使用运行状况检查中间件，对应用及其依赖项执行运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="2aed1-159">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="2aed1-160">有关更多信息，请参见 <xref:host-and-deploy/health-checks>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-160">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2aed1-161">其他资源</span><span class="sxs-lookup"><span data-stu-id="2aed1-161">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="2aed1-162">ASP.NET 托管</span><span class="sxs-lookup"><span data-stu-id="2aed1-162">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2aed1-163">一般而言，向托管环境部署 ASP.NET Core 应用需执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2aed1-163">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="2aed1-164">将已发布应用部署到托管服务器上的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2aed1-164">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="2aed1-165">设置进程管理器，该管理器在收到请求时启动应用，并在应用发生故障或服务器重启后重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="2aed1-165">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="2aed1-166">对于反向代理配置，将反向代理设置为将请求转发到应用。</span><span class="sxs-lookup"><span data-stu-id="2aed1-166">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="2aed1-167">发布到文件夹</span><span class="sxs-lookup"><span data-stu-id="2aed1-167">Publish to a folder</span></span>

<span data-ttu-id="2aed1-168">[dotnet publish](/dotnet/core/tools/dotnet-publish) 命令编译应用代码，并复制在“发布”  文件夹中运行应用所需的文件。</span><span class="sxs-lookup"><span data-stu-id="2aed1-168">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="2aed1-169">使用 Visual Studio 进行部署时，自动先执行 `dotnet publish` 步骤，再将文件复制到部署目标。</span><span class="sxs-lookup"><span data-stu-id="2aed1-169">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="2aed1-170">文件夹内容</span><span class="sxs-lookup"><span data-stu-id="2aed1-170">Folder contents</span></span>

<span data-ttu-id="2aed1-171">“发布”  文件夹包含一个或多个应用程序集文件、依赖项以及（可选）.NET 运行时。</span><span class="sxs-lookup"><span data-stu-id="2aed1-171">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="2aed1-172">.NET Core 应用可以发布为“独立式部署”  ，也可以发布为“依赖框架的部署”  。</span><span class="sxs-lookup"><span data-stu-id="2aed1-172">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="2aed1-173">如果应用是独立式，“发布”  文件夹中有包含 .NET 运行时的程序集文件。</span><span class="sxs-lookup"><span data-stu-id="2aed1-173">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="2aed1-174">如果应用依赖于框架，.NET 运行时文件就不包含在内，因为应用包含对服务器上安装的 .NET 版本的引用。</span><span class="sxs-lookup"><span data-stu-id="2aed1-174">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="2aed1-175">默认部署模型是依赖于框架的模型。</span><span class="sxs-lookup"><span data-stu-id="2aed1-175">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="2aed1-176">有关详细信息，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-176">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="2aed1-177">除了“.exe”  和“.dll”  文件以外，ASP.NET Core 应用的“发布”  文件夹通常包含配置文件、静态资产和 MVC 视图。</span><span class="sxs-lookup"><span data-stu-id="2aed1-177">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="2aed1-178">有关更多信息，请参见 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-178">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="2aed1-179">设置进程管理器</span><span class="sxs-lookup"><span data-stu-id="2aed1-179">Set up a process manager</span></span>

<span data-ttu-id="2aed1-180">ASP.NET Core 应用是一个控制台应用，在服务器启动时必须启动该应用，并且在出现故障后必须重新启动它。</span><span class="sxs-lookup"><span data-stu-id="2aed1-180">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="2aed1-181">若要自动启动和重新启动，需要使用进程管理器。</span><span class="sxs-lookup"><span data-stu-id="2aed1-181">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="2aed1-182">用于 ASP.NET Core 的最常见进程管理器是：</span><span class="sxs-lookup"><span data-stu-id="2aed1-182">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="2aed1-183">Linux</span><span class="sxs-lookup"><span data-stu-id="2aed1-183">Linux</span></span>
  * [<span data-ttu-id="2aed1-184">Nginx</span><span class="sxs-lookup"><span data-stu-id="2aed1-184">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="2aed1-185">Apache</span><span class="sxs-lookup"><span data-stu-id="2aed1-185">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="2aed1-186">Windows</span><span class="sxs-lookup"><span data-stu-id="2aed1-186">Windows</span></span>
  * [<span data-ttu-id="2aed1-187">IIS</span><span class="sxs-lookup"><span data-stu-id="2aed1-187">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="2aed1-188">Windows 服务</span><span class="sxs-lookup"><span data-stu-id="2aed1-188">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="2aed1-189">设置反向代理</span><span class="sxs-lookup"><span data-stu-id="2aed1-189">Set up a reverse proxy</span></span>

<span data-ttu-id="2aed1-190">如果应用使用 [Kestrel](xref:fundamentals/servers/kestrel) 服务器，[Nginx](xref:host-and-deploy/linux-nginx)、[Apache](xref:host-and-deploy/linux-apache) 或 [IIS](xref:host-and-deploy/iis/index) 可用作反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="2aed1-190">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="2aed1-191">反向代理服务器接收来自 Internet 的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="2aed1-191">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="2aed1-192">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="2aed1-192">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="2aed1-193">有关详细信息，请参阅[何时结合使用 Kestrel 和反向代理](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-193">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2aed1-194">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="2aed1-194">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2aed1-195">对于托管在代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="2aed1-195">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="2aed1-196">如不提供附加配置，应用可能无法访问方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="2aed1-196">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="2aed1-197">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-197">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="2aed1-198">使用 Visual Studio 和 MSBuild 自动执行部署</span><span class="sxs-lookup"><span data-stu-id="2aed1-198">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="2aed1-199">除将输出从 [dotnet publish](/dotnet/core/tools/dotnet-publish) 复制到服务器外，部署通常还需要其他任务。</span><span class="sxs-lookup"><span data-stu-id="2aed1-199">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="2aed1-200">例如，可能需要从“发布”  文件夹获取或排除额外文件。</span><span class="sxs-lookup"><span data-stu-id="2aed1-200">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="2aed1-201">Visual Studio 使用 MSBuild 进行 Web 部署，用户可以自定义 MSBuild 以在部署期间执行其他任务。</span><span class="sxs-lookup"><span data-stu-id="2aed1-201">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="2aed1-202">有关详细信息，请参阅 <xref:host-and-deploy/visual-studio-publish-profiles> 和[使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/) 一书。</span><span class="sxs-lookup"><span data-stu-id="2aed1-202">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="2aed1-203">通过[发布 Web 功能](xref:tutorials/publish-to-azure-webapp-using-vs)或[内置 Git 支持](xref:host-and-deploy/azure-apps/azure-continuous-deployment)，可以将应用从 Visual Studio 直接部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="2aed1-203">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="2aed1-204">Azure DevOps Services 支持[对 Azure 应用服务进行持续部署](/azure/devops/pipelines/targets/webapp)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-204">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="2aed1-205">有关详细信息，请参阅[通过 ASP.NET Core 和 Azure 实现 DevOps](xref:azure/devops/index)。</span><span class="sxs-lookup"><span data-stu-id="2aed1-205">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="2aed1-206">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="2aed1-206">Publish to Azure</span></span>

<span data-ttu-id="2aed1-207">有关如何使用 Visual Studio 将应用发布到 Azure 的说明，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-207">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="2aed1-208">[在 Azure 中创建 ASP.NET Core Web 应用](/azure/app-service/app-service-web-get-started-dotnet)提供了其他示例。</span><span class="sxs-lookup"><span data-stu-id="2aed1-208">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="2aed1-209">在 Windows 中使用 MSDeploy 发布</span><span class="sxs-lookup"><span data-stu-id="2aed1-209">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="2aed1-210">请参阅 <xref:host-and-deploy/visual-studio-publish-profiles>，了解如何使用 Visual Studio 发布配置文件发布应用，其中包括在 Windows 命令提示符处使用 [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 命令。</span><span class="sxs-lookup"><span data-stu-id="2aed1-210">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="2aed1-211">Internet Information Services (IIS)</span><span class="sxs-lookup"><span data-stu-id="2aed1-211">Internet Information Services (IIS)</span></span>

<span data-ttu-id="2aed1-212">要使用 web.config 文件提供的配置部署到 Internet Information Services (IIS)，请参阅  *下的文章*<xref:host-and-deploy/iis/index>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-212">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="2aed1-213">在 Web 场中托管</span><span class="sxs-lookup"><span data-stu-id="2aed1-213">Host in a web farm</span></span>

<span data-ttu-id="2aed1-214">有关如何配置在 Web 场环境中托管 ASP.NET Core 应用（例如，部署应用的多个实例以实现可伸缩性）的信息，请参阅 <xref:host-and-deploy/web-farm>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-214">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="2aed1-215">在 Docker 中托管</span><span class="sxs-lookup"><span data-stu-id="2aed1-215">Host on Docker</span></span>

<span data-ttu-id="2aed1-216">有关更多信息，请参见 <xref:host-and-deploy/docker/index>。</span><span class="sxs-lookup"><span data-stu-id="2aed1-216">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2aed1-217">其他资源</span><span class="sxs-lookup"><span data-stu-id="2aed1-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="2aed1-218">ASP.NET 托管</span><span class="sxs-lookup"><span data-stu-id="2aed1-218">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
