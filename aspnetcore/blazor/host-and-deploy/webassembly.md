---
title: 托管和部署 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: c3f537ff3b55f295db478cb097bc99023cc71a87
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326516"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="b6149-103">托管和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b6149-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="b6149-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams) 和 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="b6149-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="b6149-105">利用 [Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="b6149-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="b6149-106">将 Blazor 应用、其依赖项及 .NET 运行时并行下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="b6149-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="b6149-107">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="b6149-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="b6149-108">支持以下部署策略：</span><span class="sxs-lookup"><span data-stu-id="b6149-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="b6149-109">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="b6149-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="b6149-110">[使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="b6149-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="b6149-111">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="b6149-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="b6149-112">[独立部署](#standalone-deployment)部分介绍了此策略，包括有关将 Blazor WebAssembly 应用作为 IIS 子应用托管的信息。</span><span class="sxs-lookup"><span data-stu-id="b6149-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="b6149-113">压缩</span><span class="sxs-lookup"><span data-stu-id="b6149-113">Compression</span></span>

<span data-ttu-id="b6149-114">发布 Blazor WebAssembly 应用时，将在发布过程中对输出内容进行静态压缩，从而减小应用的大小，并免去运行时压缩的开销。</span><span class="sxs-lookup"><span data-stu-id="b6149-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="b6149-115">使用以下压缩算法：</span><span class="sxs-lookup"><span data-stu-id="b6149-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="b6149-116">[Brotli](https://tools.ietf.org/html/rfc7932)（级别最高）</span><span class="sxs-lookup"><span data-stu-id="b6149-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="b6149-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="b6149-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="b6149-118">Blazor 依赖于主机提供适当的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="b6149-119">使用 ASP.NET Core 托管项目时，主机项目能够执行内容协商并提供静态压缩文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="b6149-120">托管 Blazor WebAssembly 独立应用时，可能需要额外的工作来确保提供静态压缩文件：</span><span class="sxs-lookup"><span data-stu-id="b6149-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="b6149-121">有关 IIS `web.config` 压缩配置，请参阅 [IIS：Brotli 和 Gzip 压缩](#brotli-and-gzip-compression) 部分。</span><span class="sxs-lookup"><span data-stu-id="b6149-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="b6149-122">如果在不支持静态压缩文件内容协商的静态托管解决方案（例如 GitHub 页面）上进行托管，请考虑配置应用以提取和解码 Brotli 压缩文件：</span><span class="sxs-lookup"><span data-stu-id="b6149-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="b6149-123">从 [google/brotli GitHub repository](https://github.com/google/brotli) 中获取 JavaScript Brotli 解码器。</span><span class="sxs-lookup"><span data-stu-id="b6149-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="b6149-124">自 2020 年 9 月起，解码器文件命名为 `decode.js`，并且位于存储库的 [`js` 文件夹](https://github.com/google/brotli/tree/master/js)中。</span><span class="sxs-lookup"><span data-stu-id="b6149-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="b6149-125">[google/brotli GitHub 存储库](https://github.com/google/brotli)中的缩小版 `decode.js` 脚本 (`decode.min.js`) 中存在回归。</span><span class="sxs-lookup"><span data-stu-id="b6149-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="b6149-126">在问题[“decode.min.js 中未设置 Window.BrotliDecode”(google/brotli #844)](https://github.com/google/brotli/issues/844) 解决之前，请自行缩小脚本，或者使用 [npm 包](https://www.npmjs.com/package/brotli)。</span><span class="sxs-lookup"><span data-stu-id="b6149-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="b6149-127">本部分中的示例代码使用脚本的未缩小版。</span><span class="sxs-lookup"><span data-stu-id="b6149-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="b6149-128">更新应用以使用解码器。</span><span class="sxs-lookup"><span data-stu-id="b6149-128">Update the app to use the decoder.</span></span> <span data-ttu-id="b6149-129">将 `wwwroot/index.html` 中结束 `<body>` 标记内的标记更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="b6149-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="b6149-130">若要禁用压缩，请将 `BlazorEnableCompression` MSBuild 属性添加到应用的项目文件，并将值设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="b6149-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="b6149-131">可在命令行界面中使用以下语法将 `BlazorEnableCompression` 属性传递给 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令：</span><span class="sxs-lookup"><span data-stu-id="b6149-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="b6149-132">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="b6149-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="b6149-133">在 Blazor WebAssembly 应用中路由对页组件的请求不如在 Blazor Server 托管应用中路由请求直接。</span><span class="sxs-lookup"><span data-stu-id="b6149-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="b6149-134">假设有一个具有两个组件的 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="b6149-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="b6149-135">`Main.razor`：在应用的根目录处加载，并包含指向 `About` 组件 (`href="About"`) 的链接。</span><span class="sxs-lookup"><span data-stu-id="b6149-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="b6149-136">`About.razor`：`About` 组件。</span><span class="sxs-lookup"><span data-stu-id="b6149-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="b6149-137">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="b6149-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="b6149-138">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="b6149-138">The browser makes a request.</span></span>
1. <span data-ttu-id="b6149-139">返回默认页，通常为 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="b6149-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="b6149-140">`index.html` 启动应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="b6149-141">Blazor 的路由器进行加载，然后呈现 Razor `Main` 组件。</span><span class="sxs-lookup"><span data-stu-id="b6149-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="b6149-142">在 Main 页中，选择指向 `About` 组件的链接适用于客户端，因为 Blazor 路由器阻止浏览器在 Internet 上发出请求，针对 `About` 转到 `www.contoso.com`，并为呈现的 `About` 组件本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="b6149-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="b6149-143">针对 Blazor WebAssembly 应用中的内部终结点的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="b6149-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="b6149-144">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="b6149-144">The router handles the requests internally.</span></span>

<span data-ttu-id="b6149-145">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="b6149-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="b6149-146">应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="b6149-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="b6149-147">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 `index.html` 页。</span><span class="sxs-lookup"><span data-stu-id="b6149-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="b6149-148">如果返回 `index.html`，应用的 Blazor 路由器将接管工作并使用正确的资源响应。</span><span class="sxs-lookup"><span data-stu-id="b6149-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="b6149-149">部署到 IIS 服务器时，可以将 URL 重写模块与应用的已发布 `web.config` 文件一起使用。</span><span class="sxs-lookup"><span data-stu-id="b6149-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="b6149-150">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="b6149-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="b6149-151">使用 ASP.NET Core 进行托管部署</span><span class="sxs-lookup"><span data-stu-id="b6149-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="b6149-152">托管部署通过在 Web 服务器上运行的 [ASP.NET Core](xref:index) 应用为浏览器提供 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="b6149-153">客户端 Blazor WebAssembly 应用与服务器应用的其他任何静态 Web 资产一起发布到服务器应用的 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b6149-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="b6149-154">这两个应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="b6149-154">The two apps are deployed together.</span></span> <span data-ttu-id="b6149-155">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="b6149-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b6149-156">对于托管部署，Visual Studio 会在选择 `Hosted` 选项（使用 `dotnet new` 命令时为 `-ho|--hosted`）的情况下，包含 Blazor WebAssembly 应用项目模板（使用 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令时为 `blazorwasm` 模板） 。</span><span class="sxs-lookup"><span data-stu-id="b6149-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="b6149-157">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="b6149-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="b6149-158">若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="b6149-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="b6149-159">具有多个 Blazor WebAssembly 应用的托管部署</span><span class="sxs-lookup"><span data-stu-id="b6149-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="b6149-160">应用配置</span><span class="sxs-lookup"><span data-stu-id="b6149-160">App configuration</span></span>

<span data-ttu-id="b6149-161">若要配置托管的 Blazor 解决方案以处理多个 Blazor WebAssembly 应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="b6149-162">使用现有的托管 Blazor 解决方案，或者从 Blazor 托管的项目模板创建一个新解决方案。</span><span class="sxs-lookup"><span data-stu-id="b6149-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="b6149-163">在客户端应用的项目文件中，在 `<PropertyGroup>` 中添加一个值为 `FirstApp` 的 `<StaticWebAssetBasePath>` 属性，以设置项目静态资产的基路径：</span><span class="sxs-lookup"><span data-stu-id="b6149-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="b6149-164">向解决方案添加第二个客户端应用：</span><span class="sxs-lookup"><span data-stu-id="b6149-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="b6149-165">向解决方案的文件夹添加一个名为 `SecondClient` 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b6149-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="b6149-166">在 Blazor WebAssembly 项目模板的 `SecondClient` 文件夹中创建一个名为 `SecondBlazorApp.Client` 的 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="b6149-167">在应用的项目文件中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-167">In the app's project file:</span></span>

    * <span data-ttu-id="b6149-168">向 `<PropertyGroup>` 添加一个值为 `SecondApp` 的 `<StaticWebAssetBasePath>` 属性：</span><span class="sxs-lookup"><span data-stu-id="b6149-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="b6149-169">向 `Shared` 项目添加一个项目引用：</span><span class="sxs-lookup"><span data-stu-id="b6149-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="b6149-170">占位符 `{SOLUTION NAME}` 是解决方案的名称。</span><span class="sxs-lookup"><span data-stu-id="b6149-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="b6149-171">在服务器应用的项目文件中，为新增的客户端应用创建一个项目引用：</span><span class="sxs-lookup"><span data-stu-id="b6149-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="b6149-172">在服务器应用的 `Properties/launchSettings.json` 文件中，配置 Kestrel 配置文件 (`{SOLUTION NAME}.Server`) 的 `applicationUrl`，以访问位于端口 5001 和 5002 的客户端应用：</span><span class="sxs-lookup"><span data-stu-id="b6149-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="b6149-173">在服务器应用的 `Startup.Configure` 方法 (`Startup.cs`) 中，删除在调用 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 后显示的以下行：</span><span class="sxs-lookup"><span data-stu-id="b6149-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="b6149-174">添加将请求映射到客户端应用的中间件。</span><span class="sxs-lookup"><span data-stu-id="b6149-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="b6149-175">下面的示例将中间件配置为在以下情况下运行：</span><span class="sxs-lookup"><span data-stu-id="b6149-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="b6149-176">原始客户端应用的请求端口为 5001，或新增的客户端应用的请求端口为 5002。</span><span class="sxs-lookup"><span data-stu-id="b6149-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="b6149-177">原始客户端应用的请求主机为 `firstapp.com`，或新增的客户端应用的请求主机为 `secondapp.com`。</span><span class="sxs-lookup"><span data-stu-id="b6149-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="b6149-178">本部分中显示的示例需要其他配置以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="b6149-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="b6149-179">访问示例主机域 `firstapp.com` 和 `secondapp.com` 上的应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="b6149-180">获取客户端应用的证书以启用 TLS 安全性 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="b6149-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="b6149-181">所需的配置不在本文内容范围内，并且取决于解决方案的托管方式。</span><span class="sxs-lookup"><span data-stu-id="b6149-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="b6149-182">有关详细信息，请参阅[托管和部署文章](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="b6149-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="b6149-183">将以下代码放在先前已删除行的位置：</span><span class="sxs-lookup"><span data-stu-id="b6149-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="b6149-184">在服务器应用的天气预报控制器 (`Controllers/WeatherForecastController.cs`) 中，将到 `WeatherForecastController` 的现有路由 (`[Route("[controller]")]`) 替换为以下路由：</span><span class="sxs-lookup"><span data-stu-id="b6149-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="b6149-185">先前添加到服务器应用的 `Startup.Configure` 方法的中间件会将向 `/WeatherForecast` 发出的传入请求修改为 `/FirstApp/WeatherForecast` 或 `/SecondApp/WeatherForecast`，具体取决于端口 (5001/5002) 或域 (`firstapp.com`/`secondapp.com`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="b6149-186">为了将天气数据从服务器应用返回到客户端应用，需要前面的控制器路由。</span><span class="sxs-lookup"><span data-stu-id="b6149-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="b6149-187">静态资产和类库</span><span class="sxs-lookup"><span data-stu-id="b6149-187">Static assets and class libraries</span></span>

<span data-ttu-id="b6149-188">对于静态资产，请使用以下方法：</span><span class="sxs-lookup"><span data-stu-id="b6149-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="b6149-189">如果资产位于客户端应用的 `wwwroot` 文件夹中，请正常提供其路径：</span><span class="sxs-lookup"><span data-stu-id="b6149-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="b6149-190">如果资产位于 [Razor 类库 (RCL)](xref:blazor/components/class-libraries) 的 `wwwroot` 文件夹中，请按照 [RCL 文章](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)中的指导，在客户端应用程序中引用静态资产：</span><span class="sxs-lookup"><span data-stu-id="b6149-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6149-191">通常引用按类库提供给客户端应用的组件。</span><span class="sxs-lookup"><span data-stu-id="b6149-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="b6149-192">如果任何组件需要样式表或 JavaScript 文件，请使用以下方法之一来获取静态资产：</span><span class="sxs-lookup"><span data-stu-id="b6149-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="b6149-193">客户端应用的 `wwwroot/index.html` 文件可以链接 (`<link>`) 到静态资产。</span><span class="sxs-lookup"><span data-stu-id="b6149-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="b6149-194">组件可以使用框架的 [`Link` 组件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)获取静态资产。</span><span class="sxs-lookup"><span data-stu-id="b6149-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="b6149-195">下面的示例中演示了上述方法。</span><span class="sxs-lookup"><span data-stu-id="b6149-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b6149-196">通常引用按类库提供给客户端应用的组件。</span><span class="sxs-lookup"><span data-stu-id="b6149-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="b6149-197">如果任何组件需要样式表或 JavaScript 文件，客户端应用的 `wwwroot/index.html` 文件必须包含正确的静态资产链接。</span><span class="sxs-lookup"><span data-stu-id="b6149-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="b6149-198">下面的示例中演示了这些方法。</span><span class="sxs-lookup"><span data-stu-id="b6149-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="b6149-199">将以下 `Jeep` 组件添加到其中一个客户端应用中。</span><span class="sxs-lookup"><span data-stu-id="b6149-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="b6149-200">`Jeep` 组件使用：</span><span class="sxs-lookup"><span data-stu-id="b6149-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="b6149-201">来自客户端应用的 `wwwroot` 文件夹中的图像 (`jeep-cj.png`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="b6149-202">来自[已添加的 Razor 组件库](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` 文件夹中的图像 (`jeep-yj.png`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="b6149-203">将 `JeepImage` 库添加到解决方案中后，RCL 项目模板会自动创建示例组件 (`Component1`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="b6149-204">除非图像归你所有，否则不要公开发布车辆的图像。</span><span class="sxs-lookup"><span data-stu-id="b6149-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="b6149-205">否则，可能会侵犯版权。</span><span class="sxs-lookup"><span data-stu-id="b6149-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b6149-206">另外，还可以向库的 `Component1` 组件(`Component1.razor`) 添加库的 `jeep-yj.png` 图像。</span><span class="sxs-lookup"><span data-stu-id="b6149-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="b6149-207">若要向客户端应用的页面提供 `my-component` CSS 类，请使用框架的 [`Link` 组件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)链接到库的样式表：</span><span class="sxs-lookup"><span data-stu-id="b6149-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="b6149-208">或者，使用 [`Link` 组件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)的另一种方法是从客户端应用的 `wwwroot/index.html` 文件加载样式表。</span><span class="sxs-lookup"><span data-stu-id="b6149-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="b6149-209">这种方法使样式表可用于客户端应用中的所有组件：</span><span class="sxs-lookup"><span data-stu-id="b6149-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b6149-210">还可以向库的 `Component1` 组件 (`Component1.razor`) 添加库的 `jeep-yj.png` 图像：</span><span class="sxs-lookup"><span data-stu-id="b6149-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="b6149-211">客户端应用的 `wwwroot/index.html` 文件要求库的样式表添加以下 `<link>` 标记：</span><span class="sxs-lookup"><span data-stu-id="b6149-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="b6149-212">向客户端应用的 `NavMenu` 组件中的 `Jeep` 组件 (`Shared/NavMenu.razor`) 添加导航：</span><span class="sxs-lookup"><span data-stu-id="b6149-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="b6149-213">有关 RCL 的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="b6149-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="b6149-214">独立部署</span><span class="sxs-lookup"><span data-stu-id="b6149-214">Standalone deployment</span></span>

<span data-ttu-id="b6149-215">独立部署将 Blazor WebAssembly 应用作为客户端直接请求的一组静态文件提供。</span><span class="sxs-lookup"><span data-stu-id="b6149-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="b6149-216">任何静态文件服务器均可提供 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="b6149-217">独立部署资产将发布到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b6149-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="b6149-218">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="b6149-218">Azure App Service</span></span>

<span data-ttu-id="b6149-219">可以将 Blazor WebAssembly 应用部署到 Windows 上的 Azure 应用服务，该服务在 [IIS](#iis) 上托管应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="b6149-220">目前不支持将独立的 Blazor WebAssembly 应用部署到适用于 Linux 的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="b6149-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="b6149-221">目前无法提供用于托管应用的 Linux 服务器映像。</span><span class="sxs-lookup"><span data-stu-id="b6149-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="b6149-222">正在进行此工作以支持此场景。</span><span class="sxs-lookup"><span data-stu-id="b6149-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="b6149-223">IIS</span><span class="sxs-lookup"><span data-stu-id="b6149-223">IIS</span></span>

<span data-ttu-id="b6149-224">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="b6149-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="b6149-225">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="b6149-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="b6149-226">`/bin/Release/{TARGET FRAMEWORK}/publish` 文件夹中已创建发布的资产。</span><span class="sxs-lookup"><span data-stu-id="b6149-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="b6149-227">在 Web 服务器或托管服务上托管 `publish` 文件夹的内容。</span><span class="sxs-lookup"><span data-stu-id="b6149-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="b6149-228">web.config</span><span class="sxs-lookup"><span data-stu-id="b6149-228">web.config</span></span>

<span data-ttu-id="b6149-229">发布 Blazor 项目时，将使用以下 IIS 配置创建 `web.config` 文件：</span><span class="sxs-lookup"><span data-stu-id="b6149-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="b6149-230">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="b6149-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="b6149-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="b6149-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="b6149-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="b6149-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="b6149-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="b6149-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="b6149-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b6149-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="b6149-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b6149-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="b6149-236">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="b6149-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="b6149-237">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="b6149-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="b6149-238">提供应用的静态资产所驻留的子目录 (`wwwroot/{PATH REQUESTED}`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="b6149-239">创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 (`wwwroot/index.html`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="b6149-240">使用自定义 web.config</span><span class="sxs-lookup"><span data-stu-id="b6149-240">Use a custom web.config</span></span>

<span data-ttu-id="b6149-241">要使用自定义 `web.config` 文件，请将自定义 `web.config` 文件置于项目文件夹的根目录下。</span><span class="sxs-lookup"><span data-stu-id="b6149-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="b6149-242">将项目配置为使用应用项目文件中的 `PublishIISAssets` 发布特定于 IIS 的资源，并发布项目：</span><span class="sxs-lookup"><span data-stu-id="b6149-242">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="b6149-243">安装 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="b6149-243">Install the URL Rewrite Module</span></span>

<span data-ttu-id="b6149-244">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="b6149-244">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="b6149-245">此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。</span><span class="sxs-lookup"><span data-stu-id="b6149-245">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="b6149-246">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="b6149-246">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="b6149-247">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="b6149-247">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="b6149-248">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="b6149-248">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="b6149-249">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。</span><span class="sxs-lookup"><span data-stu-id="b6149-249">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="b6149-250">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="b6149-250">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="b6149-251">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="b6149-251">Copy the installer to the server.</span></span> <span data-ttu-id="b6149-252">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="b6149-252">Run the installer.</span></span> <span data-ttu-id="b6149-253">选择“安装”按钮，并接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="b6149-253">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="b6149-254">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="b6149-254">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="b6149-255">配置网站</span><span class="sxs-lookup"><span data-stu-id="b6149-255">Configure the website</span></span>

<span data-ttu-id="b6149-256">将网站的物理路径设置为应用的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b6149-256">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="b6149-257">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="b6149-257">The folder contains:</span></span>

* <span data-ttu-id="b6149-258">`web.config` 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="b6149-258">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="b6149-259">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="b6149-259">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="b6149-260">作为 IIS 子应用托管</span><span class="sxs-lookup"><span data-stu-id="b6149-260">Host as an IIS sub-app</span></span>

<span data-ttu-id="b6149-261">如果独立应用作为 IIS 子应用托管，请执行下列任一操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-261">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="b6149-262">禁用继承的 ASP.NET Core 模块处理程序。</span><span class="sxs-lookup"><span data-stu-id="b6149-262">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="b6149-263">通过向文件添加 `<handlers>` 部分，删除 Blazor 应用的已发布 `web.config` 文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="b6149-263">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="b6149-264">通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="b6149-264">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="b6149-265">除[配置应用的基路径](xref:blazor/host-and-deploy/index#app-base-path)外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="b6149-265">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="b6149-266">在 IIS 中配置子应用时，在应用的 `index.html` 文件中将应用基路径设置为 IIS 别名。</span><span class="sxs-lookup"><span data-stu-id="b6149-266">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="b6149-267">Brotli 和 Gzip 压缩</span><span class="sxs-lookup"><span data-stu-id="b6149-267">Brotli and Gzip compression</span></span>

<span data-ttu-id="b6149-268">通过 `web.config` 可将 IIS 配置为提供 Brotli 或 Gzip 压缩的 Blazor 资产。</span><span class="sxs-lookup"><span data-stu-id="b6149-268">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="b6149-269">有关示例配置，请参阅 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true)。</span><span class="sxs-lookup"><span data-stu-id="b6149-269">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="b6149-270">疑难解答</span><span class="sxs-lookup"><span data-stu-id="b6149-270">Troubleshooting</span></span>

<span data-ttu-id="b6149-271">如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="b6149-271">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="b6149-272">如果未安装该模块，则 IIS 无法分析 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-272">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="b6149-273">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="b6149-273">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="b6149-274">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="b6149-274">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="b6149-275">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="b6149-275">Azure Storage</span></span>

<span data-ttu-id="b6149-276">[Azure 存储](/azure/storage/)静态文件承载允许无服务器的 Blazor 应用承载。</span><span class="sxs-lookup"><span data-stu-id="b6149-276">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="b6149-277">支持自定义域名、Azure 内容分发网络 (CDN) 以及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="b6149-277">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="b6149-278">为存储帐户上的静态网站承载启用 blob 服务时：</span><span class="sxs-lookup"><span data-stu-id="b6149-278">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="b6149-279">设置“索引文档名称”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="b6149-279">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="b6149-280">设置“错误文档路径”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="b6149-280">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="b6149-281">Razor 组件和其他非文件终结点不会驻留在由 blob 服务存储的静态内容中的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="b6149-281">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="b6149-282">当收到 Blazor 路由器应处理的对这些资源之一的请求时，由 blob 服务生成的“404 - 未找到”错误会将此请求路由到“错误文档路径”。</span><span class="sxs-lookup"><span data-stu-id="b6149-282">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="b6149-283">返回 `index.html` blob，Blazor 路由器会加载并处理此路径。</span><span class="sxs-lookup"><span data-stu-id="b6149-283">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="b6149-284">如果由于文件的 `Content-Type` 标头中的 MIME 类型不正确，导致在运行时未加载文件，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-284">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="b6149-285">配置工具，用于在部署文件时设置正确的 MIME 类型（`Content-Type` 标头）。</span><span class="sxs-lookup"><span data-stu-id="b6149-285">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="b6149-286">在部署应用后更改文件的 MIME 类型（`Content-Type` 标头）。</span><span class="sxs-lookup"><span data-stu-id="b6149-286">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="b6149-287">在每个文件的存储资源管理器（Azure 门户）中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-287">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="b6149-288">右键单击该文件并选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="b6149-288">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="b6149-289">设置“ContentType”并选择“保存”按钮 。</span><span class="sxs-lookup"><span data-stu-id="b6149-289">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="b6149-290">有关更多信息，请参阅 [Azure 存储中的静态网站承载](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="b6149-290">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="b6149-291">Nginx</span><span class="sxs-lookup"><span data-stu-id="b6149-291">Nginx</span></span>

<span data-ttu-id="b6149-292">以下 `nginx.conf` 文件已简化，以展示如何配置 Nginx，以便每当在磁盘上找不到相应文件时，就发送 `index.html` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-292">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="b6149-293">使用 [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) 设置 [NGINX 突发速率限制](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)时，Blazor WebAssembly 应用可能需要一个较大的 `burst` 参数值来容纳应用发出的请求数。</span><span class="sxs-lookup"><span data-stu-id="b6149-293">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="b6149-294">首先，将值设置为不低于 60：</span><span class="sxs-lookup"><span data-stu-id="b6149-294">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="b6149-295">如果浏览器开发人员工具或网络流量工具指示请求收到“503 - 服务不可用”状态代码，则将该值调高。</span><span class="sxs-lookup"><span data-stu-id="b6149-295">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="b6149-296">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="b6149-296">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="b6149-297">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="b6149-297">Nginx in Docker</span></span>

<span data-ttu-id="b6149-298">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="b6149-298">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="b6149-299">更新 Dockerfile，以将 `nginx.config` 文件复制到容器。</span><span class="sxs-lookup"><span data-stu-id="b6149-299">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="b6149-300">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="b6149-300">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="b6149-301">Apache</span><span class="sxs-lookup"><span data-stu-id="b6149-301">Apache</span></span>

<span data-ttu-id="b6149-302">若要将 Blazor WebAssembly 应用部署到 CentOS 7 或更高版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-302">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="b6149-303">创建 Apache 配置文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-303">Create the Apache configuration file.</span></span> <span data-ttu-id="b6149-304">下面的示例展示了一个简化的配置文件 (`blazorapp.config`)：</span><span class="sxs-lookup"><span data-stu-id="b6149-304">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="b6149-305">将 Apache 配置文件放入 `/etc/httpd/conf.d/` 目录（这是 CentOS 7 中的默认 Apache 配置目录）。</span><span class="sxs-lookup"><span data-stu-id="b6149-305">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="b6149-306">将应用的文件放入 `/var/www/blazorapp` 目录（配置文件中特定于 `DocumentRoot` 的位置）。</span><span class="sxs-lookup"><span data-stu-id="b6149-306">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="b6149-307">重启 Apache 服务。</span><span class="sxs-lookup"><span data-stu-id="b6149-307">Restart the Apache service.</span></span>

<span data-ttu-id="b6149-308">有关详细信息，请参阅 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="b6149-308">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="b6149-309">GitHub 页</span><span class="sxs-lookup"><span data-stu-id="b6149-309">GitHub Pages</span></span>

<span data-ttu-id="b6149-310">要处理 URL 重写，请使用脚本添加 `wwwroot/404.html` 文件，该脚本可处理到 `index.html` 页的重定向请求。</span><span class="sxs-lookup"><span data-stu-id="b6149-310">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="b6149-311">有关示例，请参阅 [SteveSandersonMS/BlazorOnGitHubPages GitHub 存储库](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)：</span><span class="sxs-lookup"><span data-stu-id="b6149-311">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="b6149-312">[实时站点](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="b6149-312">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="b6149-313">如果使用项目站点而非组织站点，请在 `wwwroot/index.html` 中更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="b6149-313">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="b6149-314">将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`/my-repository/`）。</span><span class="sxs-lookup"><span data-stu-id="b6149-314">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="b6149-315">在 [SteveSandersonMS/BlazorOnGitHubPages GitHub 存储库](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)中，将在发布时通过 [`.github/workflows/main.yml` 配置文件](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)更新基本 `href`。</span><span class="sxs-lookup"><span data-stu-id="b6149-315">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="b6149-316">[SteveSandersonMS/BlazorOnGitHubPages GitHub 存储库](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) 不归 .NET Foundation 或 Microsoft 所有，也不由它们提供维护和支持。</span><span class="sxs-lookup"><span data-stu-id="b6149-316">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b6149-317">主机配置值</span><span class="sxs-lookup"><span data-stu-id="b6149-317">Host configuration values</span></span>

<span data-ttu-id="b6149-318">在开发环境中，[Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)可以在运行时接受以下主机配置值作为命令行参数。</span><span class="sxs-lookup"><span data-stu-id="b6149-318">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="b6149-319">内容根</span><span class="sxs-lookup"><span data-stu-id="b6149-319">Content root</span></span>

<span data-ttu-id="b6149-320">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径（[内容根目录](xref:fundamentals/index#content-root)）。</span><span class="sxs-lookup"><span data-stu-id="b6149-320">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="b6149-321">在下面的示例中，`/content-root-path` 是应用的内容根路径。</span><span class="sxs-lookup"><span data-stu-id="b6149-321">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="b6149-322">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="b6149-322">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b6149-323">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-323">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="b6149-324">在 IIS Express 配置文件中，向应用的 `launchSettings.json` 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="b6149-324">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="b6149-325">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="b6149-325">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="b6149-326">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="b6149-326">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b6149-327">在 Visual Studio 属性页中设置参数可将参数添加到 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-327">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="b6149-328">基路径</span><span class="sxs-lookup"><span data-stu-id="b6149-328">Path base</span></span>

<span data-ttu-id="b6149-329">`--pathbase` 参数可设置使用非根相对 URL 路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="b6149-329">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="b6149-330">在下面的示例中，`/relative-URL-path` 是应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="b6149-330">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="b6149-331">有关详细信息，请参阅[应用基路径](xref:blazor/host-and-deploy/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="b6149-331">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6149-332">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="b6149-332">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="b6149-333">如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="b6149-333">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="b6149-334">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="b6149-334">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b6149-335">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-335">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="b6149-336">在 IIS Express 配置文件中，向应用的 `launchSettings.json` 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="b6149-336">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="b6149-337">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="b6149-337">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="b6149-338">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="b6149-338">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b6149-339">在 Visual Studio 属性页中设置参数可将参数添加到 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-339">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="b6149-340">URL</span><span class="sxs-lookup"><span data-stu-id="b6149-340">URLs</span></span>

<span data-ttu-id="b6149-341">`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="b6149-341">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="b6149-342">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="b6149-342">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b6149-343">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-343">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="b6149-344">在 IIS Express 配置文件中，向应用的 `launchSettings.json` 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="b6149-344">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="b6149-345">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="b6149-345">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="b6149-346">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="b6149-346">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b6149-347">在 Visual Studio 属性页中设置参数可将参数添加到 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-347">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="b6149-348">配置裁边器</span><span class="sxs-lookup"><span data-stu-id="b6149-348">Configure the Trimmer</span></span>

<span data-ttu-id="b6149-349">Blazor 对每个发布版本执行中间语言 (IL) 剪裁，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="b6149-349">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="b6149-350">有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-trimmer>。</span><span class="sxs-lookup"><span data-stu-id="b6149-350">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="b6149-351">配置链接器</span><span class="sxs-lookup"><span data-stu-id="b6149-351">Configure the Linker</span></span>

<span data-ttu-id="b6149-352">Blazor 对每个发布版本执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="b6149-352">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="b6149-353">有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="b6149-353">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="b6149-354">自定义启动资源加载</span><span class="sxs-lookup"><span data-stu-id="b6149-354">Custom boot resource loading</span></span>

<span data-ttu-id="b6149-355">Blazor WebAssembly 应用可使用 `loadBootResource` 函数进行初始化，以覆盖内置的启动资源加载机制。</span><span class="sxs-lookup"><span data-stu-id="b6149-355">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="b6149-356">在以下场景中使用 `loadBootResource`：</span><span class="sxs-lookup"><span data-stu-id="b6149-356">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="b6149-357">允许用户从 CDN 加载静态资源，例如时区数据或 `dotnet.wasm`。</span><span class="sxs-lookup"><span data-stu-id="b6149-357">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="b6149-358">使用 HTTP 请求加载压缩的程序集，并将其解压缩到不支持从服务器提取压缩内容的主机的客户端上。</span><span class="sxs-lookup"><span data-stu-id="b6149-358">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="b6149-359">将每个 `fetch` 请求重定向到新名称，从而为资源提供别名。</span><span class="sxs-lookup"><span data-stu-id="b6149-359">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="b6149-360">`loadBootResource` 参数如下表所示。</span><span class="sxs-lookup"><span data-stu-id="b6149-360">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="b6149-361">参数</span><span class="sxs-lookup"><span data-stu-id="b6149-361">Parameter</span></span>    | <span data-ttu-id="b6149-362">描述</span><span class="sxs-lookup"><span data-stu-id="b6149-362">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="b6149-363">资源类型。</span><span class="sxs-lookup"><span data-stu-id="b6149-363">The type of the resource.</span></span> <span data-ttu-id="b6149-364">允许使用的类型：`assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="b6149-364">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="b6149-365">资源的名称。</span><span class="sxs-lookup"><span data-stu-id="b6149-365">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="b6149-366">资源的相对或绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="b6149-366">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="b6149-367">表示响应中的预期内容的完整性字符串。</span><span class="sxs-lookup"><span data-stu-id="b6149-367">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="b6149-368">`loadBootResource` 返回以下任何内容以替代加载过程：</span><span class="sxs-lookup"><span data-stu-id="b6149-368">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="b6149-369">URI 字符串。</span><span class="sxs-lookup"><span data-stu-id="b6149-369">URI string.</span></span> <span data-ttu-id="b6149-370">在以下示例 (`wwwroot/index.html`) 中，以下文件来自 CDN (`https://my-awesome-cdn.com/`)：</span><span class="sxs-lookup"><span data-stu-id="b6149-370">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="b6149-371">时区数据</span><span class="sxs-lookup"><span data-stu-id="b6149-371">Timezone data</span></span>

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

* <span data-ttu-id="b6149-372">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="b6149-372">`Promise<Response>`.</span></span> <span data-ttu-id="b6149-373">在标头中传递 `integrity` 参数以保持默认的完整性检查行为。</span><span class="sxs-lookup"><span data-stu-id="b6149-373">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="b6149-374">以下示例 (`wwwroot/index.html`) 将一个自定义 HTTP 标头添加到出站请求，并将 `integrity` 参数传递到 `fetch` 调用：</span><span class="sxs-lookup"><span data-stu-id="b6149-374">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="b6149-375">`null`/`undefined`，这将导致默认的加载行为。</span><span class="sxs-lookup"><span data-stu-id="b6149-375">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="b6149-376">外部源必须返回浏览器所需的 CORS 标头以允许跨域资源加载。</span><span class="sxs-lookup"><span data-stu-id="b6149-376">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="b6149-377">CDN 通常会默认提供所需的标头。</span><span class="sxs-lookup"><span data-stu-id="b6149-377">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="b6149-378">你只需指定自定义行为的类型。</span><span class="sxs-lookup"><span data-stu-id="b6149-378">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="b6149-379">框架会根据默认的加载行为加载未指定到 `loadBootResource` 的类型。</span><span class="sxs-lookup"><span data-stu-id="b6149-379">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="b6149-380">更改 DLL 文件的文件扩展名</span><span class="sxs-lookup"><span data-stu-id="b6149-380">Change the filename extension of DLL files</span></span>

<span data-ttu-id="b6149-381">如果需要更改应用的已发布 `.dll` 文件的文件扩展名，请按照本部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="b6149-381">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="b6149-382">发布应用后，使用 shell 脚本或 DevOps 生成管道将 `.dll` 文件重命名，以使用其他文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="b6149-382">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="b6149-383">将 `.dll` 文件的目标位置设为应用的已发布输出的 `wwwroot` 目录中（例如 `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`）。</span><span class="sxs-lookup"><span data-stu-id="b6149-383">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="b6149-384">在下面的示例中，重命名 `.dll` 文件，以使用 `.bin` 文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="b6149-384">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="b6149-385">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="b6149-385">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="b6149-386">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="b6149-386">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="b6149-387">在 Linux 或 macOS 上：</span><span class="sxs-lookup"><span data-stu-id="b6149-387">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="b6149-388">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="b6149-388">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="b6149-389">要使用不同于 `.bin` 的其他文件扩展名，请在前面的命令中替换 `.bin`。</span><span class="sxs-lookup"><span data-stu-id="b6149-389">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="b6149-390">若要处理压缩的 `blazor.boot.json.gz` 和 `blazor.boot.json.br` 文件，请采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="b6149-390">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="b6149-391">删除压缩的 `blazor.boot.json.gz` 和 `blazor.boot.json.br` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-391">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="b6149-392">此方法禁用压缩。</span><span class="sxs-lookup"><span data-stu-id="b6149-392">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="b6149-393">重新压缩更新后的 `blazor.boot.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-393">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="b6149-394">上述指导也适用于正在使用服务工作进程资产的情况。</span><span class="sxs-lookup"><span data-stu-id="b6149-394">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="b6149-395">删除或重新压缩 `wwwroot/service-worker-assets.js.br` 和 `wwwroot/service-worker-assets.js.gz`。</span><span class="sxs-lookup"><span data-stu-id="b6149-395">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="b6149-396">否则，浏览器中的文件完整性检查将失败。</span><span class="sxs-lookup"><span data-stu-id="b6149-396">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="b6149-397">以下 Windows 示例使用项目根目录中的 PowerShell 脚本。</span><span class="sxs-lookup"><span data-stu-id="b6149-397">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="b6149-398">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="b6149-398">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="b6149-399">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="b6149-399">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="b6149-400">在项目文件中，在发布应用后运行脚本：</span><span class="sxs-lookup"><span data-stu-id="b6149-400">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="b6149-401">重命名和延迟加载相同的程序集时，请参阅 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> 中的指南。</span><span class="sxs-lookup"><span data-stu-id="b6149-401">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="b6149-402">解决完整性检查失败</span><span class="sxs-lookup"><span data-stu-id="b6149-402">Resolve integrity check failures</span></span>

<span data-ttu-id="b6149-403">当 Blazor WebAssembly 下载应用的启动文件时，它会指示浏览器对响应执行完整性检查。</span><span class="sxs-lookup"><span data-stu-id="b6149-403">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="b6149-404">它使用 `blazor.boot.json` 文件中的信息为 `.dll`、`.wasm` 和其他文件指定预期的 SHA-256 哈希值。</span><span class="sxs-lookup"><span data-stu-id="b6149-404">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="b6149-405">这对以下原因有所帮助：</span><span class="sxs-lookup"><span data-stu-id="b6149-405">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="b6149-406">它可确保不会出现加载不一致文件集的风险，例如，在用户正在下载应用程序文件时将新部署应用到 Web 服务器的情况。</span><span class="sxs-lookup"><span data-stu-id="b6149-406">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="b6149-407">不一致的文件可能导致未定义的行为。</span><span class="sxs-lookup"><span data-stu-id="b6149-407">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="b6149-408">它可确保用户的浏览器从不缓存不一致或无效的响应，这些响应可能会阻止他们启动应用（即使他们手动刷新了页面也是如此）。</span><span class="sxs-lookup"><span data-stu-id="b6149-408">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="b6149-409">它可以安全地缓存响应，甚至无需检查服务器端更改，直到预期的 SHA-256 哈希本身发生更改，因此，后续页面加载需要较少的请求即可快速完成。</span><span class="sxs-lookup"><span data-stu-id="b6149-409">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="b6149-410">如果你的 Web 服务器返回的响应与预期的 SHA-256 哈希不匹配，你将看到类似于以下内容的错误显示在浏览器的开发人员控制台中：</span><span class="sxs-lookup"><span data-stu-id="b6149-410">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="b6149-411">在大多数情况下，这不是完整性检查本身的问题。</span><span class="sxs-lookup"><span data-stu-id="b6149-411">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="b6149-412">相反，它表示存在其他问题，并且完整性检查会警告你其他问题。</span><span class="sxs-lookup"><span data-stu-id="b6149-412">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="b6149-413">诊断完整性问题</span><span class="sxs-lookup"><span data-stu-id="b6149-413">Diagnosing integrity problems</span></span>

<span data-ttu-id="b6149-414">生成应用时，生成的 `blazor.boot.json` 清单将描述生成输出生成时启动资源（例如，`.dll`、`.wasm` 和其他文件）的 SHA-256 哈希。</span><span class="sxs-lookup"><span data-stu-id="b6149-414">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="b6149-415">只要 `blazor.boot.json` 中的 SHA-256 哈希与传递到浏览器的文件相匹配，完整性检查就会通过。</span><span class="sxs-lookup"><span data-stu-id="b6149-415">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="b6149-416">此失败的常见原因包括：</span><span class="sxs-lookup"><span data-stu-id="b6149-416">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="b6149-417">Web 服务器的响应是一个错误（例如，“404 - 找不到”或“500 - 内部服务器错误”），而不是浏览器所请求的文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-417">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="b6149-418">浏览器会将其报告为完整性检查失败，而不是响应失败。</span><span class="sxs-lookup"><span data-stu-id="b6149-418">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="b6149-419">在文件生成和传递到浏览器之间已更改文件的内容。</span><span class="sxs-lookup"><span data-stu-id="b6149-419">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="b6149-420">下面可能会发生这种情况：</span><span class="sxs-lookup"><span data-stu-id="b6149-420">This might happen:</span></span>
   * <span data-ttu-id="b6149-421">你或生成工具手动修改生成输出的情况。</span><span class="sxs-lookup"><span data-stu-id="b6149-421">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="b6149-422">部署过程的某个方面修改了文件的情况。</span><span class="sxs-lookup"><span data-stu-id="b6149-422">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="b6149-423">例如，在使用基于 Git 的部署机制时，请记住，如果你在 Windows 上提交文件并在 Linux 上检查它们，则 Git 会以透明方式将 Windows 样式的行尾转换为 Unix 样式的行尾。</span><span class="sxs-lookup"><span data-stu-id="b6149-423">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="b6149-424">更改文件行尾将更改 SHA-256 哈希。</span><span class="sxs-lookup"><span data-stu-id="b6149-424">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="b6149-425">若要避免此问题，请考虑[使用 `.gitattributes` 将生成项目视为 `binary` 文件](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)。</span><span class="sxs-lookup"><span data-stu-id="b6149-425">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="b6149-426">Web 服务器在提供文件内容的过程中对其进行修改。</span><span class="sxs-lookup"><span data-stu-id="b6149-426">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="b6149-427">例如，某些内容分发网络 (CDN) 会自动尝试[缩小](xref:client-side/bundling-and-minification#minification) HTML，从而对其进行修改。</span><span class="sxs-lookup"><span data-stu-id="b6149-427">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="b6149-428">可能需要禁用此类功能。</span><span class="sxs-lookup"><span data-stu-id="b6149-428">You may need to disable such features.</span></span>

<span data-ttu-id="b6149-429">若要诊断哪些功能适用于你的情况，请执行执行操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-429">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="b6149-430">通过读取错误消息来记下哪个文件触发错误。</span><span class="sxs-lookup"><span data-stu-id="b6149-430">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="b6149-431">打开浏览器的开发人员工具，然后在“网络”选项卡中查找。如有必要，请重新加载页面以查看请求和响应的列表。</span><span class="sxs-lookup"><span data-stu-id="b6149-431">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="b6149-432">在该列表中查找触发错误的文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-432">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="b6149-433">检查响应中的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="b6149-433">Check the HTTP status code in the response.</span></span> <span data-ttu-id="b6149-434">如果服务器返回除“200 - 正常”（或其他 2xx 状态代码）以外的任何内容，则需要诊断服务器端问题。</span><span class="sxs-lookup"><span data-stu-id="b6149-434">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="b6149-435">例如，状态代码 403 表示存在授权问题，而状态代码 500 表示服务器以未指定的方式失败。</span><span class="sxs-lookup"><span data-stu-id="b6149-435">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="b6149-436">请参阅服务器端日志以诊断和修复应用。</span><span class="sxs-lookup"><span data-stu-id="b6149-436">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="b6149-437">如果资源的状态代码为“200 - 正常”，请在浏览器的开发人员工具中查看响应内容，并检查内容是否与预期的数据匹配。</span><span class="sxs-lookup"><span data-stu-id="b6149-437">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="b6149-438">例如，常见问题是错误配置了路由，因此请求甚至返回其他文件的 `index.html` 数据。</span><span class="sxs-lookup"><span data-stu-id="b6149-438">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="b6149-439">请确保对 `.wasm` 请求的响应是 WebAssembly 二进制文件，对 `.dll` 请求的响应是 .NET 程序集二进制文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-439">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="b6149-440">如果不是，则需要诊断服务器端路由问题。</span><span class="sxs-lookup"><span data-stu-id="b6149-440">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="b6149-441">如果确认服务器返回看似正确的数据，则必须在生成文件和传递文件之间修改内容。</span><span class="sxs-lookup"><span data-stu-id="b6149-441">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="b6149-442">若要对此进行调查，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b6149-442">To investigate this:</span></span>

 * <span data-ttu-id="b6149-443">如果在生成文件后修改文件，请检查生成工具链和部署机制。</span><span class="sxs-lookup"><span data-stu-id="b6149-443">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="b6149-444">例如，在 Git 转换文件行尾时，如前所述。</span><span class="sxs-lookup"><span data-stu-id="b6149-444">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="b6149-445">如设置为动态修改响应（例如，尝试缩小 HTML），请检查 Web 服务器或 CDN 配置。</span><span class="sxs-lookup"><span data-stu-id="b6149-445">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="b6149-446">Web 服务器可以实现 HTTP 压缩（例如，返回 `content-encoding: br` 或 `content-encoding: gzip`），因为这不会影响解压缩后的结果。</span><span class="sxs-lookup"><span data-stu-id="b6149-446">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="b6149-447">但是，Web 服务器不可以修改未压缩的数据。</span><span class="sxs-lookup"><span data-stu-id="b6149-447">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="b6149-448">禁用非 PWA 应用的完整性检查</span><span class="sxs-lookup"><span data-stu-id="b6149-448">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="b6149-449">在大多数情况下，不要禁用完整性检查。</span><span class="sxs-lookup"><span data-stu-id="b6149-449">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="b6149-450">禁用完整性检查并不能解决导致意外响应的根本问题，并且会导致丢失前面列出的权益。</span><span class="sxs-lookup"><span data-stu-id="b6149-450">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="b6149-451">在某些情况下，Web 服务器无法用于返回一致的响应，但别无选择，只能禁用完整性检查。</span><span class="sxs-lookup"><span data-stu-id="b6149-451">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="b6149-452">若要禁用完整性检查，请将以下内容添加到 Blazor WebAssembly 项目的 `.csproj` 文件中的属性组：</span><span class="sxs-lookup"><span data-stu-id="b6149-452">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="b6149-453">`BlazorCacheBootResources` 还会根据 SHA-256 哈希禁用 Blazor 缓存 `.dll`、`.wasm` 和其他文件的默认行为，因为属性指示无法依靠 SHA-256 哈希来确保正确性。</span><span class="sxs-lookup"><span data-stu-id="b6149-453">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="b6149-454">即使有此设置，浏览器的普通 HTTP 缓存仍可能会缓存这些文件，但是否发生这种情况取决于你的 Web 服务器配置和它所提供的 `cache-control` 标头。</span><span class="sxs-lookup"><span data-stu-id="b6149-454">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="b6149-455">`BlazorCacheBootResources` 属性不会禁用[渐进式 Web 应用程序 (PWA)](xref:blazor/progressive-web-app) 的完整性检查。</span><span class="sxs-lookup"><span data-stu-id="b6149-455">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="b6149-456">有关 PWA 的相关指南，请参阅[禁用 PWA 的完整性检查](#disable-integrity-checking-for-pwas)部分。</span><span class="sxs-lookup"><span data-stu-id="b6149-456">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="b6149-457">禁用 PWA 的完整性检查</span><span class="sxs-lookup"><span data-stu-id="b6149-457">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="b6149-458">Blazor 的渐进式 Web 应用程序 (PWA) 模板包含建议的 `service-worker.published.js` 文件，该文件负责获取和存储应用程序文件以供脱机使用。</span><span class="sxs-lookup"><span data-stu-id="b6149-458">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="b6149-459">这是普通应用启动机制的独立进程，具有其自己单独的完整性检查逻辑。</span><span class="sxs-lookup"><span data-stu-id="b6149-459">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="b6149-460">在 `service-worker.published.js` 文件中，出现以下行：</span><span class="sxs-lookup"><span data-stu-id="b6149-460">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="b6149-461">若要禁用完整性检查，请通过将该行更改为以下行来删除 `integrity` 参数：</span><span class="sxs-lookup"><span data-stu-id="b6149-461">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="b6149-462">同样，禁用完整性检查意味着会丢失完整性检查提供的安全保证。</span><span class="sxs-lookup"><span data-stu-id="b6149-462">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="b6149-463">例如，如果用户的浏览器在你部署新版本的那一刻缓存应用，则会存在风险，它可能会缓存旧部署中的某些文件和新部署中的某些文件。</span><span class="sxs-lookup"><span data-stu-id="b6149-463">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="b6149-464">如果发生这种情况，则在部署进一步更新之前，应用程序会在中断状态下停滞。</span><span class="sxs-lookup"><span data-stu-id="b6149-464">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
