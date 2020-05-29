---
<span data-ttu-id="a7f43-101">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-103">'Blazor'</span></span>
- <span data-ttu-id="a7f43-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-104">'Identity'</span></span>
- <span data-ttu-id="a7f43-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-106">'Razor'</span></span>
- <span data-ttu-id="a7f43-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="a7f43-108">托管和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a7f43-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="a7f43-109">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams) 和 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="a7f43-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="a7f43-110">使用 [Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="a7f43-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="a7f43-111">将 Blazor 应用、其依赖项及 .NET 运行时并行下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="a7f43-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="a7f43-112">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="a7f43-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="a7f43-113">支持以下部署策略：</span><span class="sxs-lookup"><span data-stu-id="a7f43-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="a7f43-114">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="a7f43-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="a7f43-115">[使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="a7f43-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="a7f43-116">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="a7f43-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="a7f43-117">[独立部署](#standalone-deployment)部分介绍了此策略，包括有关将 Blazor WebAssembly 应用作为 IIS 子应用托管的信息。</span><span class="sxs-lookup"><span data-stu-id="a7f43-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="a7f43-118">Brotli 预压缩</span><span class="sxs-lookup"><span data-stu-id="a7f43-118">Brotli precompression</span></span>

<span data-ttu-id="a7f43-119">Blazor WebAssembly 应用发布时，会在最基本的层面上使用 [Brotli 压缩算法](https://tools.ietf.org/html/rfc7932)对输出内容进行预压缩，从而减少应用大小并消除对运行时压缩的需求。</span><span class="sxs-lookup"><span data-stu-id="a7f43-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="a7f43-120">有关 IIS web.config 压缩配置，请参阅 [IIS：Brotli 和 Gzip 压缩](#brotli-and-gzip-compression) 部分。</span><span class="sxs-lookup"><span data-stu-id="a7f43-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="a7f43-121">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="a7f43-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="a7f43-122">在 Blazor WebAssembly 应用中路由对页组件的请求不如在 Blazor Server 对托管应用中路由请求直接。</span><span class="sxs-lookup"><span data-stu-id="a7f43-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="a7f43-123">假设有一个 Blazor WebAssembly 应用包含以下两个组件：</span><span class="sxs-lookup"><span data-stu-id="a7f43-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="a7f43-124">Main.razor &ndash; 在应用的根目录处加载并包含指向 `About` 组件 (`href="About"`) 的链接。</span><span class="sxs-lookup"><span data-stu-id="a7f43-124">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="a7f43-125">About.razor &ndash; `About` 组件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-125">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="a7f43-126">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="a7f43-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="a7f43-127">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="a7f43-127">The browser makes a request.</span></span>
1. <span data-ttu-id="a7f43-128">返回默认页，通常为 index.html。</span><span class="sxs-lookup"><span data-stu-id="a7f43-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="a7f43-129">index.html 启动应用。</span><span class="sxs-lookup"><span data-stu-id="a7f43-129">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="a7f43-130"> 的路由器进行加载，然后呈现 Razor `Main` 组件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-130">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="a7f43-131">在 Main 页中，选择指向 `About` 组件的链接适用于客户端，因为 Blazor 路由器阻止浏览器在 Internet 上发出请求，针对 `About` 转到 `www.contoso.com`，并为呈现的 `About` 组件本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="a7f43-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="a7f43-132">针对 Blazor WebAssembly 应用中的内部终结点的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="a7f43-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="a7f43-133">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="a7f43-133">The router handles the requests internally.</span></span>

<span data-ttu-id="a7f43-134">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="a7f43-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="a7f43-135">应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="a7f43-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="a7f43-136">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页。</span><span class="sxs-lookup"><span data-stu-id="a7f43-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="a7f43-137">如果返回 index.html，应用的 Blazor 路由器将接管工作并使用正确的资源响应。</span><span class="sxs-lookup"><span data-stu-id="a7f43-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="a7f43-138">部署到 IIS 服务器时，可以将 URL 重写模块与应用的已发布 web.config 文件一起使用。</span><span class="sxs-lookup"><span data-stu-id="a7f43-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="a7f43-139">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="a7f43-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="a7f43-140">使用 ASP.NET Core 进行托管部署</span><span class="sxs-lookup"><span data-stu-id="a7f43-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="a7f43-141">托管部署通过在 Web 服务器上运行的 [ASP.NET Core](xref:index) 应用为浏览器提供 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="a7f43-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="a7f43-142">客户端 Blazor WebAssembly 应用将与服务器应用的任何其他静态 Web 资产一起发布到服务器应用的 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="a7f43-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="a7f43-143">这两个应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="a7f43-143">The two apps are deployed together.</span></span> <span data-ttu-id="a7f43-144">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="a7f43-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="a7f43-145">对于托管部署，Visual Studio 会在选择“托管”选项（使用 `dotnet new` 命令时为 `-ho|--hosted`）的情况下，包含 Blazor WebAssembly App 项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new)命令时为 `blazorwasm` 模板） 。</span><span class="sxs-lookup"><span data-stu-id="a7f43-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="a7f43-146">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="a7f43-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="a7f43-147">若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="a7f43-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="a7f43-148">独立部署</span><span class="sxs-lookup"><span data-stu-id="a7f43-148">Standalone deployment</span></span>

<span data-ttu-id="a7f43-149">独立部署将 Blazor WebAssembly 应用作为客户端直接请求的一组静态文件提供。</span><span class="sxs-lookup"><span data-stu-id="a7f43-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="a7f43-150">任何静态文件服务器均可提供 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="a7f43-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="a7f43-151">独立部署资产发布到 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="a7f43-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="a7f43-152">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="a7f43-152">Azure App Service</span></span>

<span data-ttu-id="a7f43-153">可以将 Blazor WebAssembly 应用部署到 Windows 上的 Azure 应用服务，该服务在 [IIS](#iis) 上托管应用。</span><span class="sxs-lookup"><span data-stu-id="a7f43-153">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="a7f43-154">目前不支持将独立的 Blazor WebAssembly 应用部署到适用于 Linux 的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="a7f43-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="a7f43-155">目前无法提供用于托管应用的 Linux 服务器映像。</span><span class="sxs-lookup"><span data-stu-id="a7f43-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="a7f43-156">正在进行此工作以支持此场景。</span><span class="sxs-lookup"><span data-stu-id="a7f43-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="a7f43-157">IIS</span><span class="sxs-lookup"><span data-stu-id="a7f43-157">IIS</span></span>

<span data-ttu-id="a7f43-158">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="a7f43-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="a7f43-159">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="a7f43-160">已发布的资产是在 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹中创建。</span><span class="sxs-lookup"><span data-stu-id="a7f43-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="a7f43-161">在 Web 服务器或托管服务上托管“publish”文件夹内容。</span><span class="sxs-lookup"><span data-stu-id="a7f43-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="a7f43-162">web.config</span><span class="sxs-lookup"><span data-stu-id="a7f43-162">web.config</span></span>

<span data-ttu-id="a7f43-163">发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="a7f43-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="a7f43-164">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="a7f43-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="a7f43-165">.dll &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="a7f43-165">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="a7f43-166">.json &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="a7f43-166">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="a7f43-167">.wasm &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="a7f43-167">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="a7f43-168">.woff &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="a7f43-168">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="a7f43-169">.woff2 &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="a7f43-169">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="a7f43-170">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="a7f43-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="a7f43-171">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="a7f43-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="a7f43-172">提供应用的静态资产驻留所在的子目录 (wwwroot/{PATH REQUESTED})。</span><span class="sxs-lookup"><span data-stu-id="a7f43-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="a7f43-173">创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 (wwwroot/index.html)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="a7f43-174">使用自定义 web.config</span><span class="sxs-lookup"><span data-stu-id="a7f43-174">Use a custom web.config</span></span>

<span data-ttu-id="a7f43-175">要使用自定义 web.config 文件，请将自定义 web.config 文件放在项目文件夹的根目录下，然后发布该项目 。</span><span class="sxs-lookup"><span data-stu-id="a7f43-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="a7f43-176">安装 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="a7f43-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="a7f43-177">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="a7f43-178">此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。</span><span class="sxs-lookup"><span data-stu-id="a7f43-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="a7f43-179">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="a7f43-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="a7f43-180">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="a7f43-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="a7f43-181">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="a7f43-182">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。</span><span class="sxs-lookup"><span data-stu-id="a7f43-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="a7f43-183">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="a7f43-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="a7f43-184">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="a7f43-184">Copy the installer to the server.</span></span> <span data-ttu-id="a7f43-185">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="a7f43-185">Run the installer.</span></span> <span data-ttu-id="a7f43-186">选择“安装”按钮，并接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="a7f43-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="a7f43-187">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="a7f43-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="a7f43-188">配置网站</span><span class="sxs-lookup"><span data-stu-id="a7f43-188">Configure the website</span></span>

<span data-ttu-id="a7f43-189">将网站的物理路径设置为应用的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a7f43-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="a7f43-190">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="a7f43-190">The folder contains:</span></span>

* <span data-ttu-id="a7f43-191">web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="a7f43-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="a7f43-192">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="a7f43-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="a7f43-193">作为 IIS 子应用托管</span><span class="sxs-lookup"><span data-stu-id="a7f43-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="a7f43-194">如果独立应用作为 IIS 子应用托管，请执行下列任一操作：</span><span class="sxs-lookup"><span data-stu-id="a7f43-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="a7f43-195">禁用继承的 ASP.NET Core 模块处理程序。</span><span class="sxs-lookup"><span data-stu-id="a7f43-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="a7f43-196">通过向文件添加 `<handlers>` 部分，删除 Blazor 应用已发布 web.config 文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="a7f43-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="a7f43-197">通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="a7f43-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="a7f43-198">除[配置应用的基路径](xref:host-and-deploy/blazor/index#app-base-path)外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="a7f43-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="a7f43-199">在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名。</span><span class="sxs-lookup"><span data-stu-id="a7f43-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="a7f43-200">Brotli 和 Gzip 压缩</span><span class="sxs-lookup"><span data-stu-id="a7f43-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="a7f43-201">通过 web.config 可将 IIS 配置为提供 Brotli 或 Gzip 压缩的 Blazor 资产。</span><span class="sxs-lookup"><span data-stu-id="a7f43-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="a7f43-202">有关示例配置，请参阅 [web.config](webassembly/_samples/web.config?raw=true)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="a7f43-203">疑难解答</span><span class="sxs-lookup"><span data-stu-id="a7f43-203">Troubleshooting</span></span>

<span data-ttu-id="a7f43-204">如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="a7f43-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="a7f43-205">如果未安装该模块，则 IIS 无法分析 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="a7f43-206">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="a7f43-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="a7f43-207">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="a7f43-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="a7f43-208">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="a7f43-208">Azure Storage</span></span>

<span data-ttu-id="a7f43-209">[Azure 存储](/azure/storage/)静态文件承载允许无服务器的 Blazor 应用承载。</span><span class="sxs-lookup"><span data-stu-id="a7f43-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="a7f43-210">支持自定义域名、Azure 内容分发网络 (CDN) 以及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a7f43-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="a7f43-211">为存储帐户上的静态网站承载启用 blob 服务时：</span><span class="sxs-lookup"><span data-stu-id="a7f43-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="a7f43-212">设置“索引文档名称”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="a7f43-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="a7f43-213">设置“错误文档路径”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="a7f43-213">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="a7f43-214"> 组件和其他非文件终结点不会驻留在由 blob 服务存储的静态内容中的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="a7f43-214"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="a7f43-215">当收到 Blazor 路由器应处理的对这些资源之一的请求时，由 blob 服务生成的“404 - 未找到”错误会将此请求路由到“错误文档路径”。</span><span class="sxs-lookup"><span data-stu-id="a7f43-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="a7f43-216">返回 Index.html blob，Blazor 路由器会加载并处理此路径。</span><span class="sxs-lookup"><span data-stu-id="a7f43-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="a7f43-217">有关更多信息，请参阅 [Azure 存储中的静态网站承载](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-217">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="a7f43-218">Nginx</span><span class="sxs-lookup"><span data-stu-id="a7f43-218">Nginx</span></span>

<span data-ttu-id="a7f43-219">以下 nginx.conf 文件已简化，以展示如何将 Nginx 配置为，每当在磁盘上找不到相应文件时，发送 index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-219">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="a7f43-220">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-220">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="a7f43-221">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="a7f43-221">Nginx in Docker</span></span>

<span data-ttu-id="a7f43-222">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="a7f43-222">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="a7f43-223">更新 Dockerfile，以将 nginx.config 文件复制到容器。</span><span class="sxs-lookup"><span data-stu-id="a7f43-223">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="a7f43-224">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="a7f43-224">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="a7f43-225">Apache</span><span class="sxs-lookup"><span data-stu-id="a7f43-225">Apache</span></span>

<span data-ttu-id="a7f43-226">若要将 Blazor WebAssembly 应用部署到 CentOS 7 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="a7f43-226">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="a7f43-227">创建 Apache 配置文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-227">Create the Apache configuration file.</span></span> <span data-ttu-id="a7f43-228">下面的示例展示了一个简化的配置文件 (*blazorapp.config*)：</span><span class="sxs-lookup"><span data-stu-id="a7f43-228">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="a7f43-229">将 Apache 配置文件放入 `/etc/httpd/conf.d/` 目录（这是 CentOS 7 中的默认 Apache 配置目录）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-229">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="a7f43-230">将应用的文件放入 `/var/www/blazorapp` 目录（配置文件中特定于 `DocumentRoot` 的位置）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-230">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="a7f43-231">重启 Apache 服务。</span><span class="sxs-lookup"><span data-stu-id="a7f43-231">Restart the Apache service.</span></span>

<span data-ttu-id="a7f43-232">有关详细信息，请参阅 [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-232">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="a7f43-233">GitHub 页</span><span class="sxs-lookup"><span data-stu-id="a7f43-233">GitHub Pages</span></span>

<span data-ttu-id="a7f43-234">要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求 。</span><span class="sxs-lookup"><span data-stu-id="a7f43-234">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="a7f43-235">关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](https://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-235">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="a7f43-236">可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。</span><span class="sxs-lookup"><span data-stu-id="a7f43-236">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="a7f43-237">如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="a7f43-237">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="a7f43-238">将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`my-repository/`）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-238">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="a7f43-239">主机配置值</span><span class="sxs-lookup"><span data-stu-id="a7f43-239">Host configuration values</span></span>

<span data-ttu-id="a7f43-240">在开发环境中，[Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)可以在运行时接受以下主机配置值作为命令行参数。</span><span class="sxs-lookup"><span data-stu-id="a7f43-240">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="a7f43-241">内容根</span><span class="sxs-lookup"><span data-stu-id="a7f43-241">Content root</span></span>

<span data-ttu-id="a7f43-242">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径（[内容根目录](xref:fundamentals/index#content-root)）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-242">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="a7f43-243">在下面的示例中，`/content-root-path` 是应用的内容根路径。</span><span class="sxs-lookup"><span data-stu-id="a7f43-243">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="a7f43-244">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="a7f43-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a7f43-245">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a7f43-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="a7f43-246">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="a7f43-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="a7f43-247">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="a7f43-247">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="a7f43-248">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="a7f43-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a7f43-249">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="a7f43-250">基路径</span><span class="sxs-lookup"><span data-stu-id="a7f43-250">Path base</span></span>

<span data-ttu-id="a7f43-251">`--pathbase` 参数可设置使用非根相对 URL 路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="a7f43-251">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="a7f43-252">在下面的示例中，`/relative-URL-path` 是应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="a7f43-252">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="a7f43-253">有关详细信息，请参阅[应用基路径](xref:host-and-deploy/blazor/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-253">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7f43-254">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-254">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="a7f43-255">如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="a7f43-255">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="a7f43-256">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="a7f43-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a7f43-257">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a7f43-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="a7f43-258">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="a7f43-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="a7f43-259">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="a7f43-259">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="a7f43-260">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="a7f43-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a7f43-261">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="a7f43-262">URL</span><span class="sxs-lookup"><span data-stu-id="a7f43-262">URLs</span></span>

<span data-ttu-id="a7f43-263">`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="a7f43-263">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="a7f43-264">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="a7f43-264">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="a7f43-265">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a7f43-265">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="a7f43-266">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="a7f43-266">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="a7f43-267">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="a7f43-267">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="a7f43-268">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="a7f43-268">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="a7f43-269">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-269">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="a7f43-270">配置链接器</span><span class="sxs-lookup"><span data-stu-id="a7f43-270">Configure the Linker</span></span>

Blazor<span data-ttu-id="a7f43-271"> 对每个发布版本执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="a7f43-271"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="a7f43-272">有关详细信息，请参阅 <xref:host-and-deploy/blazor/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="a7f43-272">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="a7f43-273">自定义启动资源加载</span><span class="sxs-lookup"><span data-stu-id="a7f43-273">Custom boot resource loading</span></span>

<span data-ttu-id="a7f43-274">Blazor WebAssembly 应用可使用 `loadBootResource` 函数进行初始化，以覆盖内置的启动资源加载机制。</span><span class="sxs-lookup"><span data-stu-id="a7f43-274">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="a7f43-275">在以下场景中使用 `loadBootResource`：</span><span class="sxs-lookup"><span data-stu-id="a7f43-275">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="a7f43-276">允许用户从 CDN 加载静态资源，例如时区数据或 dotnet wasm。</span><span class="sxs-lookup"><span data-stu-id="a7f43-276">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="a7f43-277">使用 HTTP 请求加载压缩的程序集，并将其解压缩到不支持从服务器提取压缩内容的主机的客户端上。</span><span class="sxs-lookup"><span data-stu-id="a7f43-277">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="a7f43-278">将每个 `fetch` 请求重定向到新名称，从而为资源提供别名。</span><span class="sxs-lookup"><span data-stu-id="a7f43-278">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="a7f43-279">`loadBootResource` 参数如下表所示。</span><span class="sxs-lookup"><span data-stu-id="a7f43-279">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="a7f43-280">参数</span><span class="sxs-lookup"><span data-stu-id="a7f43-280">Parameter</span></span>    | <span data-ttu-id="a7f43-281">描述</span><span class="sxs-lookup"><span data-stu-id="a7f43-281">Description</span></span> |
| ---
<span data-ttu-id="a7f43-282">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-282">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-284">'Blazor'</span></span>
- <span data-ttu-id="a7f43-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-285">'Identity'</span></span>
- <span data-ttu-id="a7f43-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-287">'Razor'</span></span>
- <span data-ttu-id="a7f43-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a7f43-289">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-289">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-291">'Blazor'</span></span>
- <span data-ttu-id="a7f43-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-292">'Identity'</span></span>
- <span data-ttu-id="a7f43-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-294">'Razor'</span></span>
- <span data-ttu-id="a7f43-295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a7f43-296">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-296">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-298">'Blazor'</span></span>
- <span data-ttu-id="a7f43-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-299">'Identity'</span></span>
- <span data-ttu-id="a7f43-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-301">'Razor'</span></span>
- <span data-ttu-id="a7f43-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a7f43-303">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-303">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-305">'Blazor'</span></span>
- <span data-ttu-id="a7f43-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-306">'Identity'</span></span>
- <span data-ttu-id="a7f43-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-308">'Razor'</span></span>
- <span data-ttu-id="a7f43-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-309">'SignalR' uid:</span></span> 

<span data-ttu-id="a7f43-310">------ | --- title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-310">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-312">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-312">'Blazor'</span></span>
- <span data-ttu-id="a7f43-313">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-313">'Identity'</span></span>
- <span data-ttu-id="a7f43-314">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-314">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-315">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-315">'Razor'</span></span>
- <span data-ttu-id="a7f43-316">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-316">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a7f43-317">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-317">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-319">'Blazor'</span></span>
- <span data-ttu-id="a7f43-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-320">'Identity'</span></span>
- <span data-ttu-id="a7f43-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-322">'Razor'</span></span>
- <span data-ttu-id="a7f43-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a7f43-324">title:“托管和部署 ASP.NET Core Blazor WebAssembly”author: description:“了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。”</span><span class="sxs-lookup"><span data-stu-id="a7f43-324">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="a7f43-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a7f43-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a7f43-326">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-326">'Blazor'</span></span>
- <span data-ttu-id="a7f43-327">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a7f43-327">'Identity'</span></span>
- <span data-ttu-id="a7f43-328">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a7f43-328">'Let's Encrypt'</span></span>
- <span data-ttu-id="a7f43-329">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a7f43-329">'Razor'</span></span>
- <span data-ttu-id="a7f43-330">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a7f43-330">'SignalR' uid:</span></span> 

<span data-ttu-id="a7f43-331">------ | | `type`       | 资源类型。</span><span class="sxs-lookup"><span data-stu-id="a7f43-331">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="a7f43-332">允许使用的类型：`assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata` | | `name`       | 资源名称。</span><span class="sxs-lookup"><span data-stu-id="a7f43-332">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="a7f43-333">| | `defaultUri` | 资源的相对或绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="a7f43-333">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="a7f43-334">| | `integrity`  | 表示响应中预期内容的完整性字符串。</span><span class="sxs-lookup"><span data-stu-id="a7f43-334">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="a7f43-335">`loadBootResource` 返回以下任何内容以替代加载过程：</span><span class="sxs-lookup"><span data-stu-id="a7f43-335">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="a7f43-336">URI 字符串。</span><span class="sxs-lookup"><span data-stu-id="a7f43-336">URI string.</span></span> <span data-ttu-id="a7f43-337">在以下示例中 (wwwroot/index.html)，以下文件来自 CDN (`https://my-awesome-cdn.com/`)：</span><span class="sxs-lookup"><span data-stu-id="a7f43-337">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="a7f43-338">dotnet.\*.js</span><span class="sxs-lookup"><span data-stu-id="a7f43-338">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="a7f43-339">dotnet.wasm</span><span class="sxs-lookup"><span data-stu-id="a7f43-339">*dotnet.wasm*</span></span>
  * <span data-ttu-id="a7f43-340">时区数据</span><span class="sxs-lookup"><span data-stu-id="a7f43-340">Timezone data</span></span>

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

* <span data-ttu-id="a7f43-341">`Promise<Response>`。</span><span class="sxs-lookup"><span data-stu-id="a7f43-341">`Promise<Response>`.</span></span> <span data-ttu-id="a7f43-342">在标头中传递 `integrity` 参数以保持默认的完整性检查行为。</span><span class="sxs-lookup"><span data-stu-id="a7f43-342">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="a7f43-343">以下示例 (wwwroot/index.html) 将一个自定义 HTTP 标头添加到出站请求，并将 `integrity` 参数传递到 `fetch` 调用：</span><span class="sxs-lookup"><span data-stu-id="a7f43-343">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="a7f43-344">`null`/`undefined`，这将导致默认的加载行为。</span><span class="sxs-lookup"><span data-stu-id="a7f43-344">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="a7f43-345">外部源必须返回浏览器所需的 CORS 标头以允许跨域资源加载。</span><span class="sxs-lookup"><span data-stu-id="a7f43-345">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="a7f43-346">CDN 通常会默认提供所需的标头。</span><span class="sxs-lookup"><span data-stu-id="a7f43-346">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="a7f43-347">你只需指定自定义行为的类型。</span><span class="sxs-lookup"><span data-stu-id="a7f43-347">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="a7f43-348">框架会根据默认的加载行为加载未指定到 `loadBootResource` 的类型。</span><span class="sxs-lookup"><span data-stu-id="a7f43-348">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="a7f43-349">更改 DLL 文件的文件扩展名</span><span class="sxs-lookup"><span data-stu-id="a7f43-349">Change the filename extension of DLL files</span></span>

<span data-ttu-id="a7f43-350">如果需要更改应用的已发布 .dll 文件的文件扩展名，请按照本部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="a7f43-350">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="a7f43-351">发布应用后，使用 shell 脚本或 DevOps 生成管道将 .dll 文件重命名，以使用其他文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="a7f43-351">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="a7f43-352">将 .dll 文件的目标位置设为应用的已发布输出的 wwwroot 目录中（例如 {CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot）  。</span><span class="sxs-lookup"><span data-stu-id="a7f43-352">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="a7f43-353">在下面的示例中，重命名 .dll 文件，以使用 .bin 文件扩展名 。</span><span class="sxs-lookup"><span data-stu-id="a7f43-353">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="a7f43-354">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="a7f43-354">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="a7f43-355">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="a7f43-355">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="a7f43-356">在 Linux 或 macOS 上：</span><span class="sxs-lookup"><span data-stu-id="a7f43-356">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="a7f43-357">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="a7f43-357">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="a7f43-358">要使用不同于 .bin 的其他文件扩展名，请在前面的命令中替换 .bin 。</span><span class="sxs-lookup"><span data-stu-id="a7f43-358">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="a7f43-359">要处理压缩的 blazor.boot.json.gz 和 blazor.boot.json.br 文件，请采用以下方法之一 ：</span><span class="sxs-lookup"><span data-stu-id="a7f43-359">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="a7f43-360">删除压缩的 blazor.boot.json.gz 和 blazor.boot.json.br 文件 。</span><span class="sxs-lookup"><span data-stu-id="a7f43-360">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="a7f43-361">此方法禁用压缩。</span><span class="sxs-lookup"><span data-stu-id="a7f43-361">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="a7f43-362">重新压缩更新后的 blazor.boot.json 文件。</span><span class="sxs-lookup"><span data-stu-id="a7f43-362">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="a7f43-363">上述指导也适用于正在使用服务工作进程资产的情况。</span><span class="sxs-lookup"><span data-stu-id="a7f43-363">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="a7f43-364">删除或重新压缩 wwwroot/service-worker-assets.js.br 和 wwwroot/service-worker-assets.js.gz。</span><span class="sxs-lookup"><span data-stu-id="a7f43-364">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="a7f43-365">否则，浏览器中的文件完整性检查将失败。</span><span class="sxs-lookup"><span data-stu-id="a7f43-365">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="a7f43-366">以下 Windows 示例使用项目根目录中的 PowerShell 脚本。</span><span class="sxs-lookup"><span data-stu-id="a7f43-366">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="a7f43-367">ChangeDLLExtensions.ps1:：</span><span class="sxs-lookup"><span data-stu-id="a7f43-367">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="a7f43-368">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="a7f43-368">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="a7f43-369">在项目文件中，在发布应用后运行脚本：</span><span class="sxs-lookup"><span data-stu-id="a7f43-369">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="a7f43-370">若要提供反馈，请访问 [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477)。</span><span class="sxs-lookup"><span data-stu-id="a7f43-370">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 