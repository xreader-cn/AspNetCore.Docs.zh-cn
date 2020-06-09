---
title: "title:'托管和部署 ASP.NET Core Blazor WebAssembly' author: guardrex description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:2020 年 5 月 28 日 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239383"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="1bbce-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1bbce-103">'Blazor'</span></span>

<span data-ttu-id="1bbce-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1bbce-104">'Identity'</span></span>

<span data-ttu-id="1bbce-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1bbce-105">'Let's Encrypt'</span></span>

* <span data-ttu-id="1bbce-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1bbce-106">'Razor'</span></span>
* <span data-ttu-id="1bbce-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span><span class="sxs-lookup"><span data-stu-id="1bbce-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span></span>

<span data-ttu-id="1bbce-108">托管和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1bbce-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

* <span data-ttu-id="1bbce-109">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams) 和 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="1bbce-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span> <span data-ttu-id="1bbce-110">使用 [Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="1bbce-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>
* <span data-ttu-id="1bbce-111">将 Blazor 应用、其依赖项及 .NET 运行时并行下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="1bbce-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span> <span data-ttu-id="1bbce-112">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="1bbce-112">The app is executed directly on the browser UI thread.</span></span>

## <a name="precompression"></a><span data-ttu-id="1bbce-113">支持以下部署策略：</span><span class="sxs-lookup"><span data-stu-id="1bbce-113">The following deployment strategies are supported:</span></span>

<span data-ttu-id="1bbce-114">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="1bbce-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="1bbce-115">[使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="1bbce-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>

* <span data-ttu-id="1bbce-116">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="1bbce-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span>
* <span data-ttu-id="1bbce-117">[独立部署](#standalone-deployment)部分介绍了此策略，包括有关将 Blazor WebAssembly 应用作为 IIS 子应用托管的信息。</span><span class="sxs-lookup"><span data-stu-id="1bbce-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

<span data-ttu-id="1bbce-118">预压缩</span><span class="sxs-lookup"><span data-stu-id="1bbce-118">Precompression</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="1bbce-119">在发布 Blazor WebAssembly 应用时，会对输出内容进行预压缩，这样可以减小应用的大小，并且无需再进行运行时压缩。</span><span class="sxs-lookup"><span data-stu-id="1bbce-119">When a Blazor WebAssembly app is published, the output is precompressed to reduce the app's size and remove the need for runtime compression.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="1bbce-120">使用以下压缩算法：</span><span class="sxs-lookup"><span data-stu-id="1bbce-120">The following compression algorithms are used:</span></span>

<span data-ttu-id="1bbce-121">[Brotli](https://tools.ietf.org/html/rfc7932)（级别最高）</span><span class="sxs-lookup"><span data-stu-id="1bbce-121">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span> [<span data-ttu-id="1bbce-122">Gzip</span><span class="sxs-lookup"><span data-stu-id="1bbce-122">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

* <span data-ttu-id="1bbce-123">若要禁用压缩，请将 `BlazorEnableCompression` MSBuild 属性添加到应用的项目文件，并将值设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="1bbce-123">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>
* <span data-ttu-id="1bbce-124">有关 IIS web.config 压缩配置，请参阅 [IIS：Brotli 和 Gzip 压缩](#brotli-and-gzip-compression) 部分。</span><span class="sxs-lookup"><span data-stu-id="1bbce-124">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

<span data-ttu-id="1bbce-125">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="1bbce-125">Rewrite URLs for correct routing</span></span>

1. <span data-ttu-id="1bbce-126">在 Blazor WebAssembly 应用中路由对页组件的请求不如在 Blazor Server 对托管应用中路由请求直接。</span><span class="sxs-lookup"><span data-stu-id="1bbce-126">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span>
1. <span data-ttu-id="1bbce-127">假设有一个 Blazor WebAssembly 应用包含以下两个组件：</span><span class="sxs-lookup"><span data-stu-id="1bbce-127">Consider a Blazor WebAssembly app with two components:</span></span>
1. <span data-ttu-id="1bbce-128">Main.razor：在应用的根目录处加载，并包含指向 `About` 组件 (`href="About"`) 的链接。</span><span class="sxs-lookup"><span data-stu-id="1bbce-128">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
1. <span data-ttu-id="1bbce-129">About.razor：`About` 组件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-129">*About.razor*: `About` component.</span></span>

<span data-ttu-id="1bbce-130">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="1bbce-130">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span> <span data-ttu-id="1bbce-131">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="1bbce-131">The browser makes a request.</span></span> <span data-ttu-id="1bbce-132">返回默认页，通常为 index.html。</span><span class="sxs-lookup"><span data-stu-id="1bbce-132">The default page is returned, which is usually *index.html*.</span></span>

<span data-ttu-id="1bbce-133">index.html 启动应用。</span><span class="sxs-lookup"><span data-stu-id="1bbce-133">*index.html* bootstraps the app.</span></span> Blazor<span data-ttu-id="1bbce-134"> 的路由器进行加载，然后呈现 Razor `Main` 组件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-134">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="1bbce-135">在 Main 页中，选择指向 `About` 组件的链接适用于客户端，因为 Blazor 路由器阻止浏览器在 Internet 上发出请求，针对 `About` 转到 `www.contoso.com`，并为呈现的 `About` 组件本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="1bbce-135">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="1bbce-136">针对 Blazor WebAssembly 应用中的内部终结点的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="1bbce-136">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span>

<span data-ttu-id="1bbce-137">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="1bbce-137">The router handles the requests internally.</span></span> <span data-ttu-id="1bbce-138">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="1bbce-138">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="1bbce-139">应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="1bbce-139">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="1bbce-140">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页。</span><span class="sxs-lookup"><span data-stu-id="1bbce-140">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span>

<span data-ttu-id="1bbce-141">如果返回 index.html，应用的 Blazor 路由器将接管工作并使用正确的资源响应。</span><span class="sxs-lookup"><span data-stu-id="1bbce-141">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span> <span data-ttu-id="1bbce-142">部署到 IIS 服务器时，可以将 URL 重写模块与应用的已发布 web.config 文件一起使用。</span><span class="sxs-lookup"><span data-stu-id="1bbce-142">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="1bbce-143">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="1bbce-143">For more information, see the [IIS](#iis) section.</span></span> <span data-ttu-id="1bbce-144">使用 ASP.NET Core 进行托管部署</span><span class="sxs-lookup"><span data-stu-id="1bbce-144">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="1bbce-145">托管部署通过在 Web 服务器上运行的 [ASP.NET Core](xref:index) 应用为浏览器提供 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="1bbce-145">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="1bbce-146">客户端 Blazor WebAssembly 应用将与服务器应用的任何其他静态 Web 资产一起发布到服务器应用的 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="1bbce-146">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="1bbce-147">这两个应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="1bbce-147">The two apps are deployed together.</span></span>

<span data-ttu-id="1bbce-148">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="1bbce-148">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="1bbce-149">对于托管部署，Visual Studio 会在选择“托管”选项（使用 `dotnet new` 命令时为 `-ho|--hosted`）的情况下，包含 Blazor WebAssembly App 项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new)命令时为 `blazorwasm` 模板） 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-149">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="1bbce-150">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="1bbce-150">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="1bbce-151">若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="1bbce-151">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="1bbce-152">独立部署</span><span class="sxs-lookup"><span data-stu-id="1bbce-152">Standalone deployment</span></span>

<span data-ttu-id="1bbce-153">独立部署将 Blazor WebAssembly 应用作为客户端直接请求的一组静态文件提供。</span><span class="sxs-lookup"><span data-stu-id="1bbce-153">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="1bbce-154">任何静态文件服务器均可提供 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1bbce-154">Any static file server is able to serve the Blazor app.</span></span> <span data-ttu-id="1bbce-155">独立部署资产发布到 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1bbce-155">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="1bbce-156">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="1bbce-156">Azure App Service</span></span>

<span data-ttu-id="1bbce-157">可以将 Blazor WebAssembly 应用部署到 Windows 上的 Azure 应用服务，该服务在 [IIS](#iis) 上托管应用。</span><span class="sxs-lookup"><span data-stu-id="1bbce-157">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span> <span data-ttu-id="1bbce-158">目前不支持将独立的 Blazor WebAssembly 应用部署到适用于 Linux 的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="1bbce-158">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span>

<span data-ttu-id="1bbce-159">目前无法提供用于托管应用的 Linux 服务器映像。</span><span class="sxs-lookup"><span data-stu-id="1bbce-159">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="1bbce-160">正在进行此工作以支持此场景。</span><span class="sxs-lookup"><span data-stu-id="1bbce-160">Work is in progress to enable this scenario.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="1bbce-161">IIS</span><span class="sxs-lookup"><span data-stu-id="1bbce-161">IIS</span></span>

<span data-ttu-id="1bbce-162">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="1bbce-162">IIS is a capable static file server for Blazor apps.</span></span>

* <span data-ttu-id="1bbce-163">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-163">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>
  * <span data-ttu-id="1bbce-164">已发布的资产是在 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹中创建。</span><span class="sxs-lookup"><span data-stu-id="1bbce-164">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>
  * <span data-ttu-id="1bbce-165">在 Web 服务器或托管服务上托管“publish”文件夹内容。</span><span class="sxs-lookup"><span data-stu-id="1bbce-165">Host the contents of the *publish* folder on the web server or hosting service.</span></span>
  * <span data-ttu-id="1bbce-166">web.config</span><span class="sxs-lookup"><span data-stu-id="1bbce-166">web.config</span></span>
  * <span data-ttu-id="1bbce-167">发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="1bbce-167">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>
  * <span data-ttu-id="1bbce-168">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="1bbce-168">MIME types are set for the following file extensions:</span></span>
* <span data-ttu-id="1bbce-169">.dll：`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="1bbce-169">*.dll*: `application/octet-stream`</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="1bbce-170">.json：`application/json`</span><span class="sxs-lookup"><span data-stu-id="1bbce-170">*.json*: `application/json`</span></span>
  * <span data-ttu-id="1bbce-171">.wasm：`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="1bbce-171">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="1bbce-172">.woff：`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1bbce-172">*.woff*: `application/font-woff`</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="1bbce-173">.woff2：`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1bbce-173">*.woff2*: `application/font-woff`</span></span>

<span data-ttu-id="1bbce-174">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="1bbce-174">HTTP compression is enabled for the following MIME types:</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="1bbce-175">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="1bbce-175">URL Rewrite Module rules are established:</span></span>

<span data-ttu-id="1bbce-176">提供应用的静态资产驻留所在的子目录 (wwwroot/{PATH REQUESTED})。</span><span class="sxs-lookup"><span data-stu-id="1bbce-176">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span> <span data-ttu-id="1bbce-177">创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 (wwwroot/index.html)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-177">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span> <span data-ttu-id="1bbce-178">使用自定义 web.config</span><span class="sxs-lookup"><span data-stu-id="1bbce-178">Use a custom web.config</span></span> <span data-ttu-id="1bbce-179">要使用自定义 web.config 文件，请将自定义 web.config 文件放在项目文件夹的根目录下，然后发布该项目 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-179">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

1. <span data-ttu-id="1bbce-180">安装 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="1bbce-180">Install the URL Rewrite Module</span></span> <span data-ttu-id="1bbce-181">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-181">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="1bbce-182">此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。</span><span class="sxs-lookup"><span data-stu-id="1bbce-182">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span>
1. <span data-ttu-id="1bbce-183">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="1bbce-183">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="1bbce-184">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="1bbce-184">Use the Web Platform Installer to install the module:</span></span> <span data-ttu-id="1bbce-185">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-185">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="1bbce-186">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。</span><span class="sxs-lookup"><span data-stu-id="1bbce-186">For the English version, select **WebPI** to download the WebPI installer.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="1bbce-187">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="1bbce-187">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>

<span data-ttu-id="1bbce-188">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="1bbce-188">Copy the installer to the server.</span></span> <span data-ttu-id="1bbce-189">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="1bbce-189">Run the installer.</span></span>

* <span data-ttu-id="1bbce-190">选择“安装”按钮，并接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="1bbce-190">Select the **Install** button and accept the license terms.</span></span>
* <span data-ttu-id="1bbce-191">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="1bbce-191">A server restart isn't required after the install completes.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="1bbce-192">配置网站</span><span class="sxs-lookup"><span data-stu-id="1bbce-192">Configure the website</span></span>

<span data-ttu-id="1bbce-193">将网站的物理路径设置为应用的文件夹。</span><span class="sxs-lookup"><span data-stu-id="1bbce-193">Set the website's **Physical path** to the app's folder.</span></span>

* <span data-ttu-id="1bbce-194">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="1bbce-194">The folder contains:</span></span>

  <span data-ttu-id="1bbce-195">web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="1bbce-195">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="1bbce-196">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="1bbce-196">The app's static asset folder.</span></span>

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

<span data-ttu-id="1bbce-197">作为 IIS 子应用托管</span><span class="sxs-lookup"><span data-stu-id="1bbce-197">Host as an IIS sub-app</span></span> <span data-ttu-id="1bbce-198">如果独立应用作为 IIS 子应用托管，请执行下列任一操作：</span><span class="sxs-lookup"><span data-stu-id="1bbce-198">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="1bbce-199">禁用继承的 ASP.NET Core 模块处理程序。</span><span class="sxs-lookup"><span data-stu-id="1bbce-199">Disable the inherited ASP.NET Core Module handler.</span></span>

<span data-ttu-id="1bbce-200">通过向文件添加 `<handlers>` 部分，删除 Blazor 应用已发布 web.config 文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="1bbce-200">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span> <span data-ttu-id="1bbce-201">通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="1bbce-201">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="1bbce-202">除[配置应用的基路径](xref:host-and-deploy/blazor/index#app-base-path)外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="1bbce-202">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

<span data-ttu-id="1bbce-203">在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名。</span><span class="sxs-lookup"><span data-stu-id="1bbce-203">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span> <span data-ttu-id="1bbce-204">Brotli 和 Gzip 压缩</span><span class="sxs-lookup"><span data-stu-id="1bbce-204">Brotli and Gzip compression</span></span> <span data-ttu-id="1bbce-205">通过 web.config 可将 IIS 配置为提供 Brotli 或 Gzip 压缩的 Blazor 资产。</span><span class="sxs-lookup"><span data-stu-id="1bbce-205">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span>

<span data-ttu-id="1bbce-206">有关示例配置，请参阅 [web.config](webassembly/_samples/web.config?raw=true)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-206">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

### <a name="azure-storage"></a><span data-ttu-id="1bbce-207">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1bbce-207">Troubleshooting</span></span>

<span data-ttu-id="1bbce-208">如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="1bbce-208">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="1bbce-209">如果未安装该模块，则 IIS 无法分析 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-209">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span>

<span data-ttu-id="1bbce-210">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="1bbce-210">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

* <span data-ttu-id="1bbce-211">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="1bbce-211">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>
* <span data-ttu-id="1bbce-212">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="1bbce-212">Azure Storage</span></span> <span data-ttu-id="1bbce-213">[Azure 存储](/azure/storage/)静态文件承载允许无服务器的 Blazor 应用承载。</span><span class="sxs-lookup"><span data-stu-id="1bbce-213">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="1bbce-214">支持自定义域名、Azure 内容分发网络 (CDN) 以及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1bbce-214">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span> <span data-ttu-id="1bbce-215">为存储帐户上的静态网站承载启用 blob 服务时：</span><span class="sxs-lookup"><span data-stu-id="1bbce-215">When the blob service is enabled for static website hosting on a storage account:</span></span>

<span data-ttu-id="1bbce-216">设置“索引文档名称”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="1bbce-216">Set the **Index document name** to `index.html`.</span></span>

* <span data-ttu-id="1bbce-217">设置“错误文档路径”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="1bbce-217">Set the **Error document path** to `index.html`.</span></span>
* Razor<span data-ttu-id="1bbce-218"> 组件和其他非文件终结点不会驻留在由 blob 服务存储的静态内容中的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="1bbce-218"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span>

  <span data-ttu-id="1bbce-219">当收到 Blazor 路由器应处理的对这些资源之一的请求时，由 blob 服务生成的“404 - 未找到”错误会将此请求路由到“错误文档路径”。</span><span class="sxs-lookup"><span data-stu-id="1bbce-219">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span>
  
  1. <span data-ttu-id="1bbce-220">返回 Index.html blob，Blazor 路由器会加载并处理此路径。</span><span class="sxs-lookup"><span data-stu-id="1bbce-220">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>
  1. <span data-ttu-id="1bbce-221">如果由于文件的 `Content-Type` 标头中的 MIME 类型不正确，导致在运行时未加载文件，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="1bbce-221">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

<span data-ttu-id="1bbce-222">配置工具，用于在部署文件时设置正确的 MIME 类型（`Content-Type` 标头）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-222">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>

### <a name="nginx"></a><span data-ttu-id="1bbce-223">在部署应用后更改文件的 MIME 类型（`Content-Type` 标头）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-223">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

<span data-ttu-id="1bbce-224">在每个文件的存储资源管理器（Azure 门户）中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1bbce-224">In Storage Explorer (Azure portal) for each file:</span></span>

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

<span data-ttu-id="1bbce-225">右键单击该文件并选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="1bbce-225">Right-click the file and select **Properties**.</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="1bbce-226">设置“ContentType”并选择“保存”按钮 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-226">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="1bbce-227">有关更多信息，请参阅 [Azure 存储中的静态网站承载](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-227">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span> <span data-ttu-id="1bbce-228">Nginx</span><span class="sxs-lookup"><span data-stu-id="1bbce-228">Nginx</span></span>

<span data-ttu-id="1bbce-229">以下 nginx.conf 文件已简化，以展示如何将 Nginx 配置为，每当在磁盘上找不到相应文件时，发送 index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-229">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="1bbce-230">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-230">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

<span data-ttu-id="1bbce-231">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="1bbce-231">Nginx in Docker</span></span>

1. <span data-ttu-id="1bbce-232">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="1bbce-232">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="1bbce-233">更新 Dockerfile，以将 nginx.config 文件复制到容器。</span><span class="sxs-lookup"><span data-stu-id="1bbce-233">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

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

1. <span data-ttu-id="1bbce-234">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="1bbce-234">Add one line to the Dockerfile, as shown in the following example:</span></span>

1. <span data-ttu-id="1bbce-235">Apache</span><span class="sxs-lookup"><span data-stu-id="1bbce-235">Apache</span></span>

1. <span data-ttu-id="1bbce-236">若要将 Blazor WebAssembly 应用部署到 CentOS 7 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="1bbce-236">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

<span data-ttu-id="1bbce-237">创建 Apache 配置文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-237">Create the Apache configuration file.</span></span>

### <a name="github-pages"></a><span data-ttu-id="1bbce-238">下面的示例展示了一个简化的配置文件 (*blazorapp.config*)：</span><span class="sxs-lookup"><span data-stu-id="1bbce-238">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

<span data-ttu-id="1bbce-239">将 Apache 配置文件放入 `/etc/httpd/conf.d/` 目录（这是 CentOS 7 中的默认 Apache 配置目录）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-239">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span> <span data-ttu-id="1bbce-240">将应用的文件放入 `/var/www/blazorapp` 目录（配置文件中特定于 `DocumentRoot` 的位置）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-240">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span> <span data-ttu-id="1bbce-241">重启 Apache 服务。</span><span class="sxs-lookup"><span data-stu-id="1bbce-241">Restart the Apache service.</span></span>

<span data-ttu-id="1bbce-242">有关详细信息，请参阅 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-242">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span> <span data-ttu-id="1bbce-243">GitHub 页</span><span class="sxs-lookup"><span data-stu-id="1bbce-243">GitHub Pages</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1bbce-244">要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-244">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span>

<span data-ttu-id="1bbce-245">关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](https://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-245">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span>

### <a name="content-root"></a><span data-ttu-id="1bbce-246">可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。</span><span class="sxs-lookup"><span data-stu-id="1bbce-246">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="1bbce-247">如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="1bbce-247">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="1bbce-248">将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`my-repository/`）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-248">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

* <span data-ttu-id="1bbce-249">主机配置值</span><span class="sxs-lookup"><span data-stu-id="1bbce-249">Host configuration values</span></span> <span data-ttu-id="1bbce-250">在开发环境中，[Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)可以在运行时接受以下主机配置值作为命令行参数。</span><span class="sxs-lookup"><span data-stu-id="1bbce-250">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="1bbce-251">内容根</span><span class="sxs-lookup"><span data-stu-id="1bbce-251">Content root</span></span> <span data-ttu-id="1bbce-252">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径（[内容根目录](xref:fundamentals/index#content-root)）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-252">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="1bbce-253">在下面的示例中，`/content-root-path` 是应用的内容根路径。</span><span class="sxs-lookup"><span data-stu-id="1bbce-253">In the following examples, `/content-root-path` is the app's content root path.</span></span> <span data-ttu-id="1bbce-254">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="1bbce-254">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="1bbce-255">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1bbce-255">From the app's directory, execute:</span></span>

<span data-ttu-id="1bbce-256">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="1bbce-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="1bbce-257">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="1bbce-257">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="1bbce-258">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="1bbce-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1bbce-259">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="1bbce-260">基路径</span><span class="sxs-lookup"><span data-stu-id="1bbce-260">Path base</span></span>

* <span data-ttu-id="1bbce-261">`--pathbase` 参数可设置使用非根相对 URL 路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="1bbce-261">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="1bbce-262">在下面的示例中，`/relative-URL-path` 是应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="1bbce-262">In the following examples, `/relative-URL-path` is the app's path base.</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="1bbce-263">有关详细信息，请参阅[应用基路径](xref:host-and-deploy/blazor/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-263">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="1bbce-264">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="1bbce-264">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="1bbce-265">如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="1bbce-265">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span> <span data-ttu-id="1bbce-266">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="1bbce-266">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="1bbce-267">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1bbce-267">From the app's directory, execute:</span></span>

<span data-ttu-id="1bbce-268">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="1bbce-268">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span>

* <span data-ttu-id="1bbce-269">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="1bbce-269">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span> <span data-ttu-id="1bbce-270">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="1bbce-270">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="1bbce-271">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-271">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="1bbce-272">URL</span><span class="sxs-lookup"><span data-stu-id="1bbce-272">URLs</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="1bbce-273">`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="1bbce-273">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span> <span data-ttu-id="1bbce-274">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="1bbce-274">Pass the argument when running the app locally at a command prompt.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="1bbce-275">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="1bbce-275">From the app's directory, execute:</span></span>

<span data-ttu-id="1bbce-276">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="1bbce-276">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="1bbce-277">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="1bbce-277">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="1bbce-278">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="1bbce-278">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span>

<span data-ttu-id="1bbce-279">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-279">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span> <span data-ttu-id="1bbce-280">配置链接器</span><span class="sxs-lookup"><span data-stu-id="1bbce-280">Configure the Linker</span></span>

* Blazor<span data-ttu-id="1bbce-281"> 对每个发布版本执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="1bbce-281"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span>
* <span data-ttu-id="1bbce-282">有关详细信息，请参阅 <xref:host-and-deploy/blazor/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="1bbce-282">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
* <span data-ttu-id="1bbce-283">自定义启动资源加载</span><span class="sxs-lookup"><span data-stu-id="1bbce-283">Custom boot resource loading</span></span>

<span data-ttu-id="1bbce-284">Blazor WebAssembly 应用可使用 `loadBootResource` 函数进行初始化，以覆盖内置的启动资源加载机制。</span><span class="sxs-lookup"><span data-stu-id="1bbce-284">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span>

| <span data-ttu-id="1bbce-285">在以下场景中使用 `loadBootResource`：</span><span class="sxs-lookup"><span data-stu-id="1bbce-285">Use `loadBootResource` for the following scenarios:</span></span>    | <span data-ttu-id="1bbce-286">允许用户从 CDN 加载静态资源，例如时区数据或 dotnet wasm。</span><span class="sxs-lookup"><span data-stu-id="1bbce-286">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="1bbce-287">使用 HTTP 请求加载压缩的程序集，并将其解压缩到不支持从服务器提取压缩内容的主机的客户端上。</span><span class="sxs-lookup"><span data-stu-id="1bbce-287">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span> <span data-ttu-id="1bbce-288">将每个 `fetch` 请求重定向到新名称，从而为资源提供别名。</span><span class="sxs-lookup"><span data-stu-id="1bbce-288">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span> |
| `name`       | <span data-ttu-id="1bbce-289">`loadBootResource` 参数如下表所示。</span><span class="sxs-lookup"><span data-stu-id="1bbce-289">`loadBootResource` parameters appear in the following table.</span></span> |
| `defaultUri` | <span data-ttu-id="1bbce-290">参数</span><span class="sxs-lookup"><span data-stu-id="1bbce-290">Parameter</span></span> |
| `integrity`  | <span data-ttu-id="1bbce-291">描述</span><span class="sxs-lookup"><span data-stu-id="1bbce-291">Description</span></span> |

<span data-ttu-id="1bbce-292">资源类型。</span><span class="sxs-lookup"><span data-stu-id="1bbce-292">The type of the resource.</span></span>

* <span data-ttu-id="1bbce-293">允许使用的类型：`assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="1bbce-293">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> <span data-ttu-id="1bbce-294">资源的名称。</span><span class="sxs-lookup"><span data-stu-id="1bbce-294">The name of the resource.</span></span>

  * <span data-ttu-id="1bbce-295">资源的相对或绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="1bbce-295">The relative or absolute URI of the resource.</span></span>
  * <span data-ttu-id="1bbce-296">表示响应中的预期内容的完整性字符串。</span><span class="sxs-lookup"><span data-stu-id="1bbce-296">The integrity string representing the expected content in the response.</span></span>
  * <span data-ttu-id="1bbce-297">`loadBootResource` 返回以下任何内容以替代加载过程：</span><span class="sxs-lookup"><span data-stu-id="1bbce-297">`loadBootResource` returns any of the following to override the loading process:</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="1bbce-298">URI 字符串。</span><span class="sxs-lookup"><span data-stu-id="1bbce-298">URI string.</span></span> <span data-ttu-id="1bbce-299">在以下示例中 (wwwroot/index.html)，以下文件来自 CDN (`https://my-awesome-cdn.com/`)：</span><span class="sxs-lookup"><span data-stu-id="1bbce-299">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  <span data-ttu-id="1bbce-300">dotnet.\*.js</span><span class="sxs-lookup"><span data-stu-id="1bbce-300">*dotnet.\*.js*</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="1bbce-301">dotnet.wasm</span><span class="sxs-lookup"><span data-stu-id="1bbce-301">*dotnet.wasm*</span></span>

<span data-ttu-id="1bbce-302">时区数据</span><span class="sxs-lookup"><span data-stu-id="1bbce-302">Timezone data</span></span> <span data-ttu-id="1bbce-303">`Promise<Response>`。</span><span class="sxs-lookup"><span data-stu-id="1bbce-303">`Promise<Response>`.</span></span>

<span data-ttu-id="1bbce-304">在标头中传递 `integrity` 参数以保持默认的完整性检查行为。</span><span class="sxs-lookup"><span data-stu-id="1bbce-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span> <span data-ttu-id="1bbce-305">以下示例 (wwwroot/index.html) 将一个自定义 HTTP 标头添加到出站请求，并将 `integrity` 参数传递到 `fetch` 调用：</span><span class="sxs-lookup"><span data-stu-id="1bbce-305">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="1bbce-306">`null`/`undefined`，这将导致默认的加载行为。</span><span class="sxs-lookup"><span data-stu-id="1bbce-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="1bbce-307">外部源必须返回浏览器所需的 CORS 标头以允许跨域资源加载。</span><span class="sxs-lookup"><span data-stu-id="1bbce-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span>

<span data-ttu-id="1bbce-308">CDN 通常会默认提供所需的标头。</span><span class="sxs-lookup"><span data-stu-id="1bbce-308">CDNs usually provide the required headers by default.</span></span> <span data-ttu-id="1bbce-309">你只需指定自定义行为的类型。</span><span class="sxs-lookup"><span data-stu-id="1bbce-309">You only need to specify types for custom behaviors.</span></span>

<span data-ttu-id="1bbce-310">框架会根据默认的加载行为加载未指定到 `loadBootResource` 的类型。</span><span class="sxs-lookup"><span data-stu-id="1bbce-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

<span data-ttu-id="1bbce-311">更改 DLL 文件的文件扩展名</span><span class="sxs-lookup"><span data-stu-id="1bbce-311">Change the filename extension of DLL files</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="1bbce-312">如果需要更改应用的已发布 .dll 文件的文件扩展名，请按照本部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="1bbce-312">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="1bbce-313">发布应用后，使用 shell 脚本或 DevOps 生成管道将 .dll 文件重命名，以使用其他文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="1bbce-313">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="1bbce-314">将 .dll 文件的目标位置设为应用的已发布输出的 wwwroot 目录中（例如 {CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot）  。</span><span class="sxs-lookup"><span data-stu-id="1bbce-314">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="1bbce-315">在下面的示例中，重命名 .dll 文件，以使用 .bin 文件扩展名 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-315">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="1bbce-316">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="1bbce-316">On Windows:</span></span>

* <span data-ttu-id="1bbce-317">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="1bbce-317">If service worker assets are also in use, add the following command:</span></span> <span data-ttu-id="1bbce-318">在 Linux 或 macOS 上：</span><span class="sxs-lookup"><span data-stu-id="1bbce-318">On Linux or macOS:</span></span>
* <span data-ttu-id="1bbce-319">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="1bbce-319">If service worker assets are also in use, add the following command:</span></span>

<span data-ttu-id="1bbce-320">要使用不同于 .bin 的其他文件扩展名，请在前面的命令中替换 .bin 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-320">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span> <span data-ttu-id="1bbce-321">要处理压缩的 blazor.boot.json.gz 和 blazor.boot.json.br 文件，请采用以下方法之一 ：</span><span class="sxs-lookup"><span data-stu-id="1bbce-321">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span> <span data-ttu-id="1bbce-322">删除压缩的 blazor.boot.json.gz 和 blazor.boot.json.br 文件 。</span><span class="sxs-lookup"><span data-stu-id="1bbce-322">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span>

<span data-ttu-id="1bbce-323">此方法禁用压缩。</span><span class="sxs-lookup"><span data-stu-id="1bbce-323">Compression is disabled with this approach.</span></span>

<span data-ttu-id="1bbce-324">重新压缩更新后的 blazor.boot.json 文件。</span><span class="sxs-lookup"><span data-stu-id="1bbce-324">Recompress the updated *blazor.boot.json* file.</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="1bbce-325">上述指导也适用于正在使用服务工作进程资产的情况。</span><span class="sxs-lookup"><span data-stu-id="1bbce-325">The preceding guidance also applies when service worker assets are in use.</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="1bbce-326">删除或重新压缩 wwwroot/service-worker-assets.js.br 和 wwwroot/service-worker-assets.js.gz。</span><span class="sxs-lookup"><span data-stu-id="1bbce-326">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="1bbce-327">否则，浏览器中的文件完整性检查将失败。</span><span class="sxs-lookup"><span data-stu-id="1bbce-327">Otherwise, file integrity checks fail in the browser.</span></span>
 
