---
title: 托管和部署 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: ea2c625f424447209a362cdc58bdb18be061e47f
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511348"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="f6373-103">托管和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f6373-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="f6373-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f6373-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f6373-105">使用 [Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="f6373-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="f6373-106">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="f6373-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="f6373-107">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="f6373-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="f6373-108">支持以下部署策略：</span><span class="sxs-lookup"><span data-stu-id="f6373-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="f6373-109">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="f6373-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="f6373-110">[使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="f6373-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="f6373-111">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="f6373-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="f6373-112">[独立部署](#standalone-deployment)部分介绍了此策略，包括有关将 Blazor WebAssembly 应用作为 IIS 子应用托管的信息。</span><span class="sxs-lookup"><span data-stu-id="f6373-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="f6373-113">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="f6373-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="f6373-114">在 Blazor WebAssembly 应用中路由对页组件的请求不如在 Blazor Server 对托管应用中路由请求直接。</span><span class="sxs-lookup"><span data-stu-id="f6373-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="f6373-115">假设有一个 Blazor WebAssembly 应用包含以下两个组件：</span><span class="sxs-lookup"><span data-stu-id="f6373-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="f6373-116">Main.razor  &ndash; 在应用的根目录处加载并包含指向 `About` 组件 (`href="About"`) 的链接。</span><span class="sxs-lookup"><span data-stu-id="f6373-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="f6373-117">About.razor  &ndash; `About` 组件。</span><span class="sxs-lookup"><span data-stu-id="f6373-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="f6373-118">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="f6373-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="f6373-119">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="f6373-119">The browser makes a request.</span></span>
1. <span data-ttu-id="f6373-120">返回默认页，通常为 index.html  。</span><span class="sxs-lookup"><span data-stu-id="f6373-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="f6373-121">index.html 启动应用  。</span><span class="sxs-lookup"><span data-stu-id="f6373-121">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="f6373-122"> 的路由器进行加载，然后呈现 Razor `Main` 组件。</span><span class="sxs-lookup"><span data-stu-id="f6373-122">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="f6373-123">在 Main 页中，选择指向 `About` 组件的链接适用于客户端，因为 Blazor 路由器阻止浏览器在 Internet 上发出请求，针对 `About` 转到 `www.contoso.com`，并为呈现的 `About` 组件本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="f6373-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="f6373-124">针对 Blazor WebAssembly 应用中的  内部终结点的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="f6373-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="f6373-125">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="f6373-125">The router handles the requests internally.</span></span>

<span data-ttu-id="f6373-126">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f6373-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="f6373-127">应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”  响应。</span><span class="sxs-lookup"><span data-stu-id="f6373-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="f6373-128">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页  。</span><span class="sxs-lookup"><span data-stu-id="f6373-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="f6373-129">如果返回 index.html，应用的 Blazor 路由器将接管工作并使用正确的资源响应  。</span><span class="sxs-lookup"><span data-stu-id="f6373-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="f6373-130">部署到 IIS 服务器时，可以将 URL 重写模块与应用的已发布 web.config  文件一起使用。</span><span class="sxs-lookup"><span data-stu-id="f6373-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="f6373-131">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="f6373-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="f6373-132">使用 ASP.NET Core 进行托管部署</span><span class="sxs-lookup"><span data-stu-id="f6373-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="f6373-133">托管部署  通过在 Web 服务器上运行的 [ASP.NET Core](xref:index) 应用为浏览器提供 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="f6373-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="f6373-134">客户端 Blazor WebAssembly 应用将与服务器应用的任何其他静态 Web 资产一起发布到服务器应用的 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="f6373-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="f6373-135">这两个应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="f6373-135">The two apps are deployed together.</span></span> <span data-ttu-id="f6373-136">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="f6373-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="f6373-137">对于托管部署，Visual Studio 会在选择“托管”选项（使用 `dotnet new` 命令时为 `-ho|--hosted`）的情况下，包含 Blazor WebAssembly App 项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new)命令时为 `blazorwasm` 模板）   。</span><span class="sxs-lookup"><span data-stu-id="f6373-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="f6373-138">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="f6373-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="f6373-139">若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="f6373-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="f6373-140">独立部署</span><span class="sxs-lookup"><span data-stu-id="f6373-140">Standalone deployment</span></span>

<span data-ttu-id="f6373-141">独立部署将 Blazor WebAssembly 应用作为客户端直接请求的一组静态文件提供  。</span><span class="sxs-lookup"><span data-stu-id="f6373-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="f6373-142">任何静态文件服务器均可提供 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="f6373-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="f6373-143">独立部署资产发布到 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="f6373-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="f6373-144">IIS</span><span class="sxs-lookup"><span data-stu-id="f6373-144">IIS</span></span>

<span data-ttu-id="f6373-145">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="f6373-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="f6373-146">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="f6373-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="f6373-147">已发布的资产是在 /bin/Release/{TARGET FRAMEWORK}/publish  文件夹中创建。</span><span class="sxs-lookup"><span data-stu-id="f6373-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="f6373-148">在 Web 服务器或托管服务上托管“publish”文件夹内容  。</span><span class="sxs-lookup"><span data-stu-id="f6373-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="f6373-149">web.config</span><span class="sxs-lookup"><span data-stu-id="f6373-149">web.config</span></span>

<span data-ttu-id="f6373-150">发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config  文件：</span><span class="sxs-lookup"><span data-stu-id="f6373-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="f6373-151">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="f6373-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="f6373-152">.dll  &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="f6373-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="f6373-153">.json  &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="f6373-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="f6373-154">.wasm  &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="f6373-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="f6373-155">.woff  &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="f6373-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="f6373-156">.woff2  &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="f6373-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="f6373-157">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="f6373-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="f6373-158">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="f6373-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="f6373-159">提供应用的静态资产驻留所在的子目录 (wwwroot/{PATH REQUESTED})  。</span><span class="sxs-lookup"><span data-stu-id="f6373-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="f6373-160">创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 (wwwroot/index.html)  。</span><span class="sxs-lookup"><span data-stu-id="f6373-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="f6373-161">安装 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="f6373-161">Install the URL Rewrite Module</span></span>

<span data-ttu-id="f6373-162">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="f6373-162">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="f6373-163">此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。</span><span class="sxs-lookup"><span data-stu-id="f6373-163">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="f6373-164">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="f6373-164">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="f6373-165">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="f6373-165">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="f6373-166">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="f6373-166">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="f6373-167">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序  。</span><span class="sxs-lookup"><span data-stu-id="f6373-167">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="f6373-168">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="f6373-168">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="f6373-169">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="f6373-169">Copy the installer to the server.</span></span> <span data-ttu-id="f6373-170">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f6373-170">Run the installer.</span></span> <span data-ttu-id="f6373-171">选择“安装”按钮，并接受许可条款  。</span><span class="sxs-lookup"><span data-stu-id="f6373-171">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="f6373-172">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="f6373-172">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="f6373-173">配置网站</span><span class="sxs-lookup"><span data-stu-id="f6373-173">Configure the website</span></span>

<span data-ttu-id="f6373-174">将网站的物理路径设置为应用的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="f6373-174">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="f6373-175">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="f6373-175">The folder contains:</span></span>

* <span data-ttu-id="f6373-176">web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型  。</span><span class="sxs-lookup"><span data-stu-id="f6373-176">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="f6373-177">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="f6373-177">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="f6373-178">作为 IIS 子应用托管</span><span class="sxs-lookup"><span data-stu-id="f6373-178">Host as an IIS sub-app</span></span>

<span data-ttu-id="f6373-179">如果独立应用作为 IIS 子应用托管，请执行下列任一操作：</span><span class="sxs-lookup"><span data-stu-id="f6373-179">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="f6373-180">禁用继承的 ASP.NET Core 模块处理程序。</span><span class="sxs-lookup"><span data-stu-id="f6373-180">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="f6373-181">通过向文件添加 `<handlers>` 部分，删除 Blazor 应用已发布 web.config  文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="f6373-181">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="f6373-182">通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="f6373-182">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="f6373-183">除[配置应用的基路径](xref:host-and-deploy/blazor/index#app-base-path)外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="f6373-183">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="f6373-184">在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名  。</span><span class="sxs-lookup"><span data-stu-id="f6373-184">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="f6373-185">疑难解答</span><span class="sxs-lookup"><span data-stu-id="f6373-185">Troubleshooting</span></span>

<span data-ttu-id="f6373-186">如果你看到“500 - 内部服务器错误”  ，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="f6373-186">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="f6373-187">如果未安装该模块，则 IIS 无法分析 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="f6373-187">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="f6373-188">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="f6373-188">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="f6373-189">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="f6373-189">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="f6373-190">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="f6373-190">Azure Storage</span></span>

<span data-ttu-id="f6373-191">[Azure 存储](/azure/storage/)静态文件承载允许无服务器的 Blazor 应用承载。</span><span class="sxs-lookup"><span data-stu-id="f6373-191">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="f6373-192">支持自定义域名、Azure 内容分发网络 (CDN) 以及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="f6373-192">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="f6373-193">为存储帐户上的静态网站承载启用 blob 服务时：</span><span class="sxs-lookup"><span data-stu-id="f6373-193">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="f6373-194">设置“索引文档名称”  到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="f6373-194">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="f6373-195">设置“错误文档路径”  到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="f6373-195">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="f6373-196">Razor 组件和其他非文件终结点不会驻留在由 blob 服务存储的静态内容中的物理路径。</span><span class="sxs-lookup"><span data-stu-id="f6373-196">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="f6373-197">当收到 Blazor 路由器应处理的对这些资源之一的请求时，由 blob 服务生成的“404 - 未找到”  错误会将此请求路由到“错误文档路径”  。</span><span class="sxs-lookup"><span data-stu-id="f6373-197">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="f6373-198">返回 Index.html  blob，Blazor 路由器会加载并处理此路径。</span><span class="sxs-lookup"><span data-stu-id="f6373-198">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="f6373-199">有关更多信息，请参阅 [Azure 存储中的静态网站承载](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="f6373-199">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="f6373-200">Nginx</span><span class="sxs-lookup"><span data-stu-id="f6373-200">Nginx</span></span>

<span data-ttu-id="f6373-201">以下 nginx.conf  文件已简化，以展示如何将 Nginx 配置为，每当在磁盘上找不到相应文件时，发送 index.html  文件。</span><span class="sxs-lookup"><span data-stu-id="f6373-201">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="f6373-202">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="f6373-202">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="f6373-203">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="f6373-203">Nginx in Docker</span></span>

<span data-ttu-id="f6373-204">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="f6373-204">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="f6373-205">更新 Dockerfile，以将 nginx.config 文件复制到容器  。</span><span class="sxs-lookup"><span data-stu-id="f6373-205">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="f6373-206">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="f6373-206">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="f6373-207">Apache</span><span class="sxs-lookup"><span data-stu-id="f6373-207">Apache</span></span>

<span data-ttu-id="f6373-208">若要将 Blazor WebAssembly 应用部署到 CentOS 7 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="f6373-208">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="f6373-209">创建 Apache 配置文件。</span><span class="sxs-lookup"><span data-stu-id="f6373-209">Create the Apache configuration file.</span></span> <span data-ttu-id="f6373-210">下面的示例展示了一个简化的配置文件 (*blazorapp.config*)：</span><span class="sxs-lookup"><span data-stu-id="f6373-210">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="f6373-211">将 Apache 配置文件放入 `/etc/httpd/conf.d/` 目录（这是 CentOS 7 中的默认 Apache 配置目录）。</span><span class="sxs-lookup"><span data-stu-id="f6373-211">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="f6373-212">将应用的文件放入 `/var/www/blazorapp` 目录（配置文件中特定于 `DocumentRoot` 的位置）。</span><span class="sxs-lookup"><span data-stu-id="f6373-212">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="f6373-213">重启 Apache 服务。</span><span class="sxs-lookup"><span data-stu-id="f6373-213">Restart the Apache service.</span></span>

<span data-ttu-id="f6373-214">有关详细信息，请参阅 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="f6373-214">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="f6373-215">GitHub 页</span><span class="sxs-lookup"><span data-stu-id="f6373-215">GitHub Pages</span></span>

<span data-ttu-id="f6373-216">要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求   。</span><span class="sxs-lookup"><span data-stu-id="f6373-216">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="f6373-217">关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](https://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。</span><span class="sxs-lookup"><span data-stu-id="f6373-217">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="f6373-218">可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。</span><span class="sxs-lookup"><span data-stu-id="f6373-218">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="f6373-219">如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记  。</span><span class="sxs-lookup"><span data-stu-id="f6373-219">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="f6373-220">将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`my-repository/`）。</span><span class="sxs-lookup"><span data-stu-id="f6373-220">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="f6373-221">主机配置值</span><span class="sxs-lookup"><span data-stu-id="f6373-221">Host configuration values</span></span>

<span data-ttu-id="f6373-222">在开发环境中，[Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)可以在运行时接受以下主机配置值作为命令行参数。</span><span class="sxs-lookup"><span data-stu-id="f6373-222">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="f6373-223">内容根</span><span class="sxs-lookup"><span data-stu-id="f6373-223">Content root</span></span>

<span data-ttu-id="f6373-224">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径（[内容根目录](xref:fundamentals/index#content-root)）。</span><span class="sxs-lookup"><span data-stu-id="f6373-224">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="f6373-225">在下面的示例中，`/content-root-path` 是应用的内容根路径。</span><span class="sxs-lookup"><span data-stu-id="f6373-225">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="f6373-226">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="f6373-226">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="f6373-227">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6373-227">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="f6373-228">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目   。</span><span class="sxs-lookup"><span data-stu-id="f6373-228">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="f6373-229">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="f6373-229">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="f6373-230">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数    。</span><span class="sxs-lookup"><span data-stu-id="f6373-230">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="f6373-231">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="f6373-231">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="f6373-232">基路径</span><span class="sxs-lookup"><span data-stu-id="f6373-232">Path base</span></span>

<span data-ttu-id="f6373-233">`--pathbase` 参数可设置使用非根相对 URL 路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="f6373-233">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="f6373-234">在下面的示例中，`/relative-URL-path` 是应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="f6373-234">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="f6373-235">有关详细信息，请参阅[应用基路径](xref:host-and-deploy/blazor/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="f6373-235">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f6373-236">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="f6373-236">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="f6373-237">如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="f6373-237">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="f6373-238">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="f6373-238">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="f6373-239">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6373-239">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="f6373-240">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目   。</span><span class="sxs-lookup"><span data-stu-id="f6373-240">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="f6373-241">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="f6373-241">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="f6373-242">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数    。</span><span class="sxs-lookup"><span data-stu-id="f6373-242">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="f6373-243">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="f6373-243">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="f6373-244">URL</span><span class="sxs-lookup"><span data-stu-id="f6373-244">URLs</span></span>

<span data-ttu-id="f6373-245">`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="f6373-245">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="f6373-246">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="f6373-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="f6373-247">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f6373-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="f6373-248">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目   。</span><span class="sxs-lookup"><span data-stu-id="f6373-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="f6373-249">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="f6373-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="f6373-250">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数    。</span><span class="sxs-lookup"><span data-stu-id="f6373-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="f6373-251">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="f6373-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="f6373-252">配置链接器</span><span class="sxs-lookup"><span data-stu-id="f6373-252">Configure the Linker</span></span>

Blazor<span data-ttu-id="f6373-253"> 对每个发布版本执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="f6373-253"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="f6373-254">有关详细信息，请参阅 <xref:host-and-deploy/blazor/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="f6373-254">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
