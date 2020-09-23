---
title: 托管和部署 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器和 GitHub 页来托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
ms.openlocfilehash: dadf6076e7f07c07381856aa225667a6eb38046a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080311"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="cf022-103">托管和部署 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cf022-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="cf022-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams) 和 [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="cf022-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="cf022-105">利用 [Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)：</span><span class="sxs-lookup"><span data-stu-id="cf022-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="cf022-106">将 Blazor 应用、其依赖项及 .NET 运行时并行下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="cf022-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="cf022-107">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="cf022-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="cf022-108">支持以下部署策略：</span><span class="sxs-lookup"><span data-stu-id="cf022-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="cf022-109">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="cf022-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="cf022-110">[使用 ASP.NET Core 进行托管部署](#hosted-deployment-with-aspnet-core)部分中介绍了此策略。</span><span class="sxs-lookup"><span data-stu-id="cf022-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="cf022-111">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="cf022-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="cf022-112">[独立部署](#standalone-deployment)部分介绍了此策略，包括有关将 Blazor WebAssembly 应用作为 IIS 子应用托管的信息。</span><span class="sxs-lookup"><span data-stu-id="cf022-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="cf022-113">压缩</span><span class="sxs-lookup"><span data-stu-id="cf022-113">Compression</span></span>

<span data-ttu-id="cf022-114">发布 Blazor WebAssembly 应用时，将在发布过程中对输出内容进行静态压缩，从而减小应用的大小，并免去运行时压缩的开销。</span><span class="sxs-lookup"><span data-stu-id="cf022-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="cf022-115">使用以下压缩算法：</span><span class="sxs-lookup"><span data-stu-id="cf022-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="cf022-116">[Brotli](https://tools.ietf.org/html/rfc7932)（级别最高）</span><span class="sxs-lookup"><span data-stu-id="cf022-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="cf022-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="cf022-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="cf022-118">Blazor 依赖于主机提供适当的压缩文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="cf022-119">使用 ASP.NET Core 托管项目时，主机项目能够执行内容协商并提供静态压缩文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="cf022-120">托管 Blazor WebAssembly 独立应用时，可能需要额外的工作来确保提供静态压缩文件：</span><span class="sxs-lookup"><span data-stu-id="cf022-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="cf022-121">有关 IIS `web.config` 压缩配置，请参阅 [IIS：Brotli 和 Gzip 压缩](#brotli-and-gzip-compression) 部分。</span><span class="sxs-lookup"><span data-stu-id="cf022-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="cf022-122">如果在不支持静态压缩文件内容协商的静态托管解决方案（例如 GitHub 页面）上进行托管，请考虑配置应用以提取和解码 Brotli 压缩文件：</span><span class="sxs-lookup"><span data-stu-id="cf022-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="cf022-123">从 [google/brotli GitHub repository](https://github.com/google/brotli) 中获取 JavaScript Brotli 解码器。</span><span class="sxs-lookup"><span data-stu-id="cf022-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="cf022-124">自 2020 年 7 月起，解码器文件被命名为 `decode.min.js`，并且位于存储库的 [`js` 文件夹](https://github.com/google/brotli/tree/master/js)中。</span><span class="sxs-lookup"><span data-stu-id="cf022-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="cf022-125">更新应用以使用解码器。</span><span class="sxs-lookup"><span data-stu-id="cf022-125">Update the app to use the decoder.</span></span> <span data-ttu-id="cf022-126">将 `wwwroot/index.html` 中结束 `<body>` 标记内的标记更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="cf022-126">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="cf022-127">若要禁用压缩，请将 `BlazorEnableCompression` MSBuild 属性添加到应用的项目文件，并将值设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="cf022-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="cf022-128">可在命令行界面中使用以下语法将 `BlazorEnableCompression` 属性传递给 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令：</span><span class="sxs-lookup"><span data-stu-id="cf022-128">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="cf022-129">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="cf022-129">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="cf022-130">在 Blazor WebAssembly 应用中路由对页组件的请求不如在 Blazor Server 托管应用中路由请求直接。</span><span class="sxs-lookup"><span data-stu-id="cf022-130">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="cf022-131">假设有一个具有两个组件的 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="cf022-131">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="cf022-132">`Main.razor`：在应用的根目录处加载，并包含指向 `About` 组件 (`href="About"`) 的链接。</span><span class="sxs-lookup"><span data-stu-id="cf022-132">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="cf022-133">`About.razor`：`About` 组件。</span><span class="sxs-lookup"><span data-stu-id="cf022-133">`About.razor`: `About` component.</span></span>

<span data-ttu-id="cf022-134">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="cf022-134">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="cf022-135">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="cf022-135">The browser makes a request.</span></span>
1. <span data-ttu-id="cf022-136">返回默认页，通常为 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="cf022-136">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="cf022-137">`index.html` 启动应用。</span><span class="sxs-lookup"><span data-stu-id="cf022-137">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="cf022-138">Blazor 的路由器进行加载，然后呈现 Razor `Main` 组件。</span><span class="sxs-lookup"><span data-stu-id="cf022-138">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="cf022-139">在 Main 页中，选择指向 `About` 组件的链接适用于客户端，因为 Blazor 路由器阻止浏览器在 Internet 上发出请求，针对 `About` 转到 `www.contoso.com`，并为呈现的 `About` 组件本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="cf022-139">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="cf022-140">针对 Blazor WebAssembly 应用中的内部终结点的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="cf022-140">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="cf022-141">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="cf022-141">The router handles the requests internally.</span></span>

<span data-ttu-id="cf022-142">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="cf022-142">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="cf022-143">应用的 Internet 主机上不存在此类资源，所以返回的是“404 - 找不到”响应。</span><span class="sxs-lookup"><span data-stu-id="cf022-143">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="cf022-144">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 `index.html` 页。</span><span class="sxs-lookup"><span data-stu-id="cf022-144">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="cf022-145">如果返回 `index.html`，应用的 Blazor 路由器将接管工作并使用正确的资源响应。</span><span class="sxs-lookup"><span data-stu-id="cf022-145">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="cf022-146">部署到 IIS 服务器时，可以将 URL 重写模块与应用的已发布 `web.config` 文件一起使用。</span><span class="sxs-lookup"><span data-stu-id="cf022-146">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="cf022-147">有关详细信息，请参阅 [IIS](#iis) 部分。</span><span class="sxs-lookup"><span data-stu-id="cf022-147">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="cf022-148">使用 ASP.NET Core 进行托管部署</span><span class="sxs-lookup"><span data-stu-id="cf022-148">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="cf022-149">托管部署通过在 Web 服务器上运行的 [ASP.NET Core](xref:index) 应用为浏览器提供 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="cf022-149">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="cf022-150">客户端 Blazor WebAssembly 应用与服务器应用的其他任何静态 Web 资产一起发布到服务器应用的 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="cf022-150">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="cf022-151">这两个应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="cf022-151">The two apps are deployed together.</span></span> <span data-ttu-id="cf022-152">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="cf022-152">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="cf022-153">对于托管部署，Visual Studio 会在选择 `Hosted` 选项（使用 `dotnet new` 命令时为 `-ho|--hosted`）的情况下，包含 Blazor WebAssembly 应用项目模板（使用 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令时为 `blazorwasm` 模板） 。</span><span class="sxs-lookup"><span data-stu-id="cf022-153">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="cf022-154">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="cf022-154">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="cf022-155">若要了解如何部署到 Azure 应用服务，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="cf022-155">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="cf022-156">具有多个 Blazor WebAssembly 应用的托管部署</span><span class="sxs-lookup"><span data-stu-id="cf022-156">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="cf022-157">应用配置</span><span class="sxs-lookup"><span data-stu-id="cf022-157">App configuration</span></span>

<span data-ttu-id="cf022-158">若要配置托管的 Blazor 解决方案以处理多个 Blazor WebAssembly 应用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-158">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="cf022-159">使用现有的托管 Blazor 解决方案，或者从 Blazor 托管的项目模板创建一个新解决方案。</span><span class="sxs-lookup"><span data-stu-id="cf022-159">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="cf022-160">在客户端应用的项目文件中，在 `<PropertyGroup>` 中添加一个值为 `FirstApp` 的 `<StaticWebAssetBasePath>` 属性，以设置项目静态资产的基路径：</span><span class="sxs-lookup"><span data-stu-id="cf022-160">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="cf022-161">向解决方案添加第二个客户端应用：</span><span class="sxs-lookup"><span data-stu-id="cf022-161">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="cf022-162">向解决方案的文件夹添加一个名为 `SecondClient` 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="cf022-162">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="cf022-163">在 Blazor WebAssembly 项目模板的 `SecondClient` 文件夹中创建一个名为 `SecondBlazorApp.Client` 的 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="cf022-163">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="cf022-164">在应用的项目文件中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-164">In the app's project file:</span></span>

    * <span data-ttu-id="cf022-165">向 `<PropertyGroup>` 添加一个值为 `SecondApp` 的 `<StaticWebAssetBasePath>` 属性：</span><span class="sxs-lookup"><span data-stu-id="cf022-165">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="cf022-166">向 `Shared` 项目添加一个项目引用：</span><span class="sxs-lookup"><span data-stu-id="cf022-166">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="cf022-167">占位符 `{SOLUTION NAME}` 是解决方案的名称。</span><span class="sxs-lookup"><span data-stu-id="cf022-167">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="cf022-168">在服务器应用的项目文件中，为新增的客户端应用创建一个项目引用：</span><span class="sxs-lookup"><span data-stu-id="cf022-168">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="cf022-169">在服务器应用的 `Properties/launchSettings.json` 文件中，配置 Kestrel 配置文件 (`{SOLUTION NAME}.Server`) 的 `applicationUrl`，以访问位于端口 5001 和 5002 的客户端应用：</span><span class="sxs-lookup"><span data-stu-id="cf022-169">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="cf022-170">在服务器应用的 `Startup.Configure` 方法 (`Startup.cs`) 中，删除在调用 <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> 后显示的以下行：</span><span class="sxs-lookup"><span data-stu-id="cf022-170">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="cf022-171">添加将请求映射到客户端应用的中间件。</span><span class="sxs-lookup"><span data-stu-id="cf022-171">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="cf022-172">下面的示例将中间件配置为在以下情况下运行：</span><span class="sxs-lookup"><span data-stu-id="cf022-172">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="cf022-173">原始客户端应用的请求端口为 5001，或新增的客户端应用的请求端口为 5002。</span><span class="sxs-lookup"><span data-stu-id="cf022-173">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="cf022-174">原始客户端应用的请求主机为 `firstapp.com`，或新增的客户端应用的请求主机为 `secondapp.com`。</span><span class="sxs-lookup"><span data-stu-id="cf022-174">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="cf022-175">本部分中显示的示例需要其他配置以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="cf022-175">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="cf022-176">访问示例主机域 `firstapp.com` 和 `secondapp.com` 上的应用。</span><span class="sxs-lookup"><span data-stu-id="cf022-176">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="cf022-177">获取客户端应用的证书以启用 TLS 安全性 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="cf022-177">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="cf022-178">所需的配置不在本文内容范围内，并且取决于解决方案的托管方式。</span><span class="sxs-lookup"><span data-stu-id="cf022-178">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="cf022-179">有关详细信息，请参阅[托管和部署文章](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="cf022-179">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="cf022-180">将以下代码放在先前已删除行的位置：</span><span class="sxs-lookup"><span data-stu-id="cf022-180">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="cf022-181">在服务器应用的天气预报控制器 (`Controllers/WeatherForecastController.cs`) 中，将到 `WeatherForecastController` 的现有路由 (`[Route("[controller]")]`) 替换为以下路由：</span><span class="sxs-lookup"><span data-stu-id="cf022-181">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="cf022-182">先前添加到服务器应用的 `Startup.Configure` 方法的中间件会将向 `/WeatherForecast` 发出的传入请求修改为 `/FirstApp/WeatherForecast` 或 `/SecondApp/WeatherForecast`，具体取决于端口 (5001/5002) 或域 (`firstapp.com`/`secondapp.com`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-182">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="cf022-183">为了将天气数据从服务器应用返回到客户端应用，需要前面的控制器路由。</span><span class="sxs-lookup"><span data-stu-id="cf022-183">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="cf022-184">静态资产和类库</span><span class="sxs-lookup"><span data-stu-id="cf022-184">Static assets and class libraries</span></span>

<span data-ttu-id="cf022-185">对于静态资产，请使用以下方法：</span><span class="sxs-lookup"><span data-stu-id="cf022-185">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="cf022-186">如果资产位于客户端应用的 `wwwroot` 文件夹中，请正常提供其路径：</span><span class="sxs-lookup"><span data-stu-id="cf022-186">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="cf022-187">如果资产位于 [Razor 类库 (RCL)](xref:blazor/components/class-libraries) 的 `wwwroot` 文件夹中，请按照 [RCL 文章](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)中的指导，在客户端应用程序中引用静态资产：</span><span class="sxs-lookup"><span data-stu-id="cf022-187">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cf022-188">通常引用按类库提供给客户端应用的组件。</span><span class="sxs-lookup"><span data-stu-id="cf022-188">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="cf022-189">如果任何组件需要样式表或 JavaScript 文件，请使用以下方法之一来获取静态资产：</span><span class="sxs-lookup"><span data-stu-id="cf022-189">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="cf022-190">客户端应用的 `wwwroot/index.html` 文件可以链接 (`<link>`) 到静态资产。</span><span class="sxs-lookup"><span data-stu-id="cf022-190">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="cf022-191">组件可以使用框架的 [`Link` 组件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)获取静态资产。</span><span class="sxs-lookup"><span data-stu-id="cf022-191">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="cf022-192">下面的示例中演示了上述方法。</span><span class="sxs-lookup"><span data-stu-id="cf022-192">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cf022-193">通常引用按类库提供给客户端应用的组件。</span><span class="sxs-lookup"><span data-stu-id="cf022-193">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="cf022-194">如果任何组件需要样式表或 JavaScript 文件，客户端应用的 `wwwroot/index.html` 文件必须包含正确的静态资产链接。</span><span class="sxs-lookup"><span data-stu-id="cf022-194">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="cf022-195">下面的示例中演示了这些方法。</span><span class="sxs-lookup"><span data-stu-id="cf022-195">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="cf022-196">将以下 `Jeep` 组件添加到其中一个客户端应用中。</span><span class="sxs-lookup"><span data-stu-id="cf022-196">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="cf022-197">`Jeep` 组件使用：</span><span class="sxs-lookup"><span data-stu-id="cf022-197">The `Jeep` component uses:</span></span>

* <span data-ttu-id="cf022-198">来自客户端应用的 `wwwroot` 文件夹中的图像 (`jeep-cj.png`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-198">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="cf022-199">来自[已添加的 Razor 组件库](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` 文件夹中的图像 (`jeep-yj.png`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-199">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="cf022-200">将 `JeepImage` 库添加到解决方案中后，RCL 项目模板会自动创建示例组件 (`Component1`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-200">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="cf022-201">除非图像归你所有，否则不要公开发布车辆的图像。</span><span class="sxs-lookup"><span data-stu-id="cf022-201">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="cf022-202">否则，可能会侵犯版权。</span><span class="sxs-lookup"><span data-stu-id="cf022-202">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cf022-203">另外，还可以向库的 `Component1` 组件(`Component1.razor`) 添加库的 `jeep-yj.png` 图像。</span><span class="sxs-lookup"><span data-stu-id="cf022-203">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="cf022-204">若要向客户端应用的页面提供 `my-component` CSS 类，请使用框架的 [`Link` 组件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)链接到库的样式表：</span><span class="sxs-lookup"><span data-stu-id="cf022-204">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="cf022-205">或者，使用 [`Link` 组件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)的另一种方法是从客户端应用的 `wwwroot/index.html` 文件加载样式表。</span><span class="sxs-lookup"><span data-stu-id="cf022-205">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="cf022-206">这种方法使样式表可用于客户端应用中的所有组件：</span><span class="sxs-lookup"><span data-stu-id="cf022-206">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cf022-207">还可以向库的 `Component1` 组件 (`Component1.razor`) 添加库的 `jeep-yj.png` 图像：</span><span class="sxs-lookup"><span data-stu-id="cf022-207">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="cf022-208">客户端应用的 `wwwroot/index.html` 文件要求库的样式表添加以下 `<link>` 标记：</span><span class="sxs-lookup"><span data-stu-id="cf022-208">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="cf022-209">向客户端应用的 `NavMenu` 组件中的 `Jeep` 组件 (`Shared/NavMenu.razor`) 添加导航：</span><span class="sxs-lookup"><span data-stu-id="cf022-209">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="cf022-210">有关 RCL 的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="cf022-210">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="cf022-211">独立部署</span><span class="sxs-lookup"><span data-stu-id="cf022-211">Standalone deployment</span></span>

<span data-ttu-id="cf022-212">独立部署将 Blazor WebAssembly 应用作为客户端直接请求的一组静态文件提供。</span><span class="sxs-lookup"><span data-stu-id="cf022-212">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="cf022-213">任何静态文件服务器均可提供 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="cf022-213">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="cf022-214">独立部署资产将发布到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="cf022-214">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="cf022-215">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="cf022-215">Azure App Service</span></span>

<span data-ttu-id="cf022-216">可以将 Blazor WebAssembly 应用部署到 Windows 上的 Azure 应用服务，该服务在 [IIS](#iis) 上托管应用。</span><span class="sxs-lookup"><span data-stu-id="cf022-216">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="cf022-217">目前不支持将独立的 Blazor WebAssembly 应用部署到适用于 Linux 的 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="cf022-217">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="cf022-218">目前无法提供用于托管应用的 Linux 服务器映像。</span><span class="sxs-lookup"><span data-stu-id="cf022-218">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="cf022-219">正在进行此工作以支持此场景。</span><span class="sxs-lookup"><span data-stu-id="cf022-219">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="cf022-220">IIS</span><span class="sxs-lookup"><span data-stu-id="cf022-220">IIS</span></span>

<span data-ttu-id="cf022-221">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="cf022-221">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="cf022-222">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="cf022-222">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="cf022-223">`/bin/Release/{TARGET FRAMEWORK}/publish` 文件夹中已创建发布的资产。</span><span class="sxs-lookup"><span data-stu-id="cf022-223">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="cf022-224">在 Web 服务器或托管服务上托管 `publish` 文件夹的内容。</span><span class="sxs-lookup"><span data-stu-id="cf022-224">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="cf022-225">web.config</span><span class="sxs-lookup"><span data-stu-id="cf022-225">web.config</span></span>

<span data-ttu-id="cf022-226">发布 Blazor 项目时，将使用以下 IIS 配置创建 `web.config` 文件：</span><span class="sxs-lookup"><span data-stu-id="cf022-226">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="cf022-227">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="cf022-227">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="cf022-228">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="cf022-228">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="cf022-229">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="cf022-229">`.json`: `application/json`</span></span>
  * <span data-ttu-id="cf022-230">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="cf022-230">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="cf022-231">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cf022-231">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="cf022-232">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="cf022-232">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="cf022-233">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="cf022-233">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="cf022-234">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="cf022-234">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="cf022-235">提供应用的静态资产所驻留的子目录 (`wwwroot/{PATH REQUESTED}`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-235">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="cf022-236">创建 SPA 回退路由，以便非文件资产请求能够重定向到应用的静态资产文件夹中的默认文档 (`wwwroot/index.html`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-236">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="cf022-237">使用自定义 web.config</span><span class="sxs-lookup"><span data-stu-id="cf022-237">Use a custom web.config</span></span>

<span data-ttu-id="cf022-238">要使用自定义 `web.config` 文件，请将自定义 `web.config` 文件放在项目文件夹的根目录下，然后发布该项目。</span><span class="sxs-lookup"><span data-stu-id="cf022-238">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="cf022-239">安装 URL 重写模块</span><span class="sxs-lookup"><span data-stu-id="cf022-239">Install the URL Rewrite Module</span></span>

<span data-ttu-id="cf022-240">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="cf022-240">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="cf022-241">此模块默认不安装，且不适用于安装为 Web 服务器 (IIS) 角色服务功能。</span><span class="sxs-lookup"><span data-stu-id="cf022-241">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="cf022-242">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="cf022-242">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="cf022-243">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="cf022-243">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="cf022-244">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="cf022-244">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="cf022-245">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。</span><span class="sxs-lookup"><span data-stu-id="cf022-245">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="cf022-246">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="cf022-246">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="cf022-247">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="cf022-247">Copy the installer to the server.</span></span> <span data-ttu-id="cf022-248">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="cf022-248">Run the installer.</span></span> <span data-ttu-id="cf022-249">选择“安装”按钮，并接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="cf022-249">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="cf022-250">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="cf022-250">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="cf022-251">配置网站</span><span class="sxs-lookup"><span data-stu-id="cf022-251">Configure the website</span></span>

<span data-ttu-id="cf022-252">将网站的物理路径设置为应用的文件夹。</span><span class="sxs-lookup"><span data-stu-id="cf022-252">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="cf022-253">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="cf022-253">The folder contains:</span></span>

* <span data-ttu-id="cf022-254">`web.config` 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="cf022-254">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="cf022-255">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="cf022-255">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="cf022-256">作为 IIS 子应用托管</span><span class="sxs-lookup"><span data-stu-id="cf022-256">Host as an IIS sub-app</span></span>

<span data-ttu-id="cf022-257">如果独立应用作为 IIS 子应用托管，请执行下列任一操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-257">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="cf022-258">禁用继承的 ASP.NET Core 模块处理程序。</span><span class="sxs-lookup"><span data-stu-id="cf022-258">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="cf022-259">通过向文件添加 `<handlers>` 部分，删除 Blazor 应用的已发布 `web.config` 文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="cf022-259">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="cf022-260">通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="cf022-260">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="cf022-261">除[配置应用的基路径](xref:blazor/host-and-deploy/index#app-base-path)外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="cf022-261">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="cf022-262">在 IIS 中配置子应用时，在应用的 `index.html` 文件中将应用基路径设置为 IIS 别名。</span><span class="sxs-lookup"><span data-stu-id="cf022-262">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="cf022-263">Brotli 和 Gzip 压缩</span><span class="sxs-lookup"><span data-stu-id="cf022-263">Brotli and Gzip compression</span></span>

<span data-ttu-id="cf022-264">通过 `web.config` 可将 IIS 配置为提供 Brotli 或 Gzip 压缩的 Blazor 资产。</span><span class="sxs-lookup"><span data-stu-id="cf022-264">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="cf022-265">有关示例配置，请参阅 [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true)。</span><span class="sxs-lookup"><span data-stu-id="cf022-265">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="cf022-266">疑难解答</span><span class="sxs-lookup"><span data-stu-id="cf022-266">Troubleshooting</span></span>

<span data-ttu-id="cf022-267">如果你看到“500 - 内部服务器错误”，且 IIS 管理器在尝试访问网站配置时抛出错误，请确认是否已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="cf022-267">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="cf022-268">如果未安装该模块，则 IIS 无法分析 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-268">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="cf022-269">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="cf022-269">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="cf022-270">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="cf022-270">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="cf022-271">Azure 存储</span><span class="sxs-lookup"><span data-stu-id="cf022-271">Azure Storage</span></span>

<span data-ttu-id="cf022-272">[Azure 存储](/azure/storage/)静态文件承载允许无服务器的 Blazor 应用承载。</span><span class="sxs-lookup"><span data-stu-id="cf022-272">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="cf022-273">支持自定义域名、Azure 内容分发网络 (CDN) 以及 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="cf022-273">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="cf022-274">为存储帐户上的静态网站承载启用 blob 服务时：</span><span class="sxs-lookup"><span data-stu-id="cf022-274">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="cf022-275">设置“索引文档名称”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="cf022-275">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="cf022-276">设置“错误文档路径”到 `index.html`。</span><span class="sxs-lookup"><span data-stu-id="cf022-276">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="cf022-277">Razor 组件和其他非文件终结点不会驻留在由 blob 服务存储的静态内容中的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="cf022-277">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="cf022-278">当收到 Blazor 路由器应处理的对这些资源之一的请求时，由 blob 服务生成的“404 - 未找到”错误会将此请求路由到“错误文档路径”。</span><span class="sxs-lookup"><span data-stu-id="cf022-278">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="cf022-279">返回 `index.html` blob，Blazor 路由器会加载并处理此路径。</span><span class="sxs-lookup"><span data-stu-id="cf022-279">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="cf022-280">如果由于文件的 `Content-Type` 标头中的 MIME 类型不正确，导致在运行时未加载文件，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-280">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="cf022-281">配置工具，用于在部署文件时设置正确的 MIME 类型（`Content-Type` 标头）。</span><span class="sxs-lookup"><span data-stu-id="cf022-281">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="cf022-282">在部署应用后更改文件的 MIME 类型（`Content-Type` 标头）。</span><span class="sxs-lookup"><span data-stu-id="cf022-282">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="cf022-283">在每个文件的存储资源管理器（Azure 门户）中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-283">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="cf022-284">右键单击该文件并选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="cf022-284">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="cf022-285">设置“ContentType”并选择“保存”按钮 。</span><span class="sxs-lookup"><span data-stu-id="cf022-285">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="cf022-286">有关更多信息，请参阅 [Azure 存储中的静态网站承载](/azure/storage/blobs/storage-blob-static-website)。</span><span class="sxs-lookup"><span data-stu-id="cf022-286">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="cf022-287">Nginx</span><span class="sxs-lookup"><span data-stu-id="cf022-287">Nginx</span></span>

<span data-ttu-id="cf022-288">以下 `nginx.conf` 文件已简化，以展示如何配置 Nginx，以便每当在磁盘上找不到相应文件时，就发送 `index.html` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-288">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="cf022-289">使用 [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) 设置 [NGINX 突发速率限制](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)时，Blazor WebAssembly 应用可能需要一个较大的 `burst` 参数值来容纳应用发出的请求数。</span><span class="sxs-lookup"><span data-stu-id="cf022-289">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="cf022-290">首先，将值设置为不低于 60：</span><span class="sxs-lookup"><span data-stu-id="cf022-290">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="cf022-291">如果浏览器开发人员工具或网络流量工具指示请求收到“503 - 服务不可用”状态代码，则将该值调高。</span><span class="sxs-lookup"><span data-stu-id="cf022-291">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="cf022-292">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="cf022-292">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="cf022-293">Docker 中的 Nginx</span><span class="sxs-lookup"><span data-stu-id="cf022-293">Nginx in Docker</span></span>

<span data-ttu-id="cf022-294">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="cf022-294">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="cf022-295">更新 Dockerfile，以将 `nginx.config` 文件复制到容器。</span><span class="sxs-lookup"><span data-stu-id="cf022-295">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="cf022-296">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="cf022-296">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="cf022-297">Apache</span><span class="sxs-lookup"><span data-stu-id="cf022-297">Apache</span></span>

<span data-ttu-id="cf022-298">若要将 Blazor WebAssembly 应用部署到 CentOS 7 或更高版本，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-298">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="cf022-299">创建 Apache 配置文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-299">Create the Apache configuration file.</span></span> <span data-ttu-id="cf022-300">下面的示例展示了一个简化的配置文件 (`blazorapp.config`)：</span><span class="sxs-lookup"><span data-stu-id="cf022-300">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="cf022-301">将 Apache 配置文件放入 `/etc/httpd/conf.d/` 目录（这是 CentOS 7 中的默认 Apache 配置目录）。</span><span class="sxs-lookup"><span data-stu-id="cf022-301">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="cf022-302">将应用的文件放入 `/var/www/blazorapp` 目录（配置文件中特定于 `DocumentRoot` 的位置）。</span><span class="sxs-lookup"><span data-stu-id="cf022-302">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="cf022-303">重启 Apache 服务。</span><span class="sxs-lookup"><span data-stu-id="cf022-303">Restart the Apache service.</span></span>

<span data-ttu-id="cf022-304">有关详细信息，请参阅 [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) 和 [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html)。</span><span class="sxs-lookup"><span data-stu-id="cf022-304">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="cf022-305">GitHub 页</span><span class="sxs-lookup"><span data-stu-id="cf022-305">GitHub Pages</span></span>

<span data-ttu-id="cf022-306">要处理 URL 重写，请使用脚本添加 `wwwroot/404.html` 文件，该脚本可处理到 `index.html` 页的重定向请求。</span><span class="sxs-lookup"><span data-stu-id="cf022-306">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="cf022-307">有关示例，请参阅 [SteveSandersonMS/BlazorOnGitHubPages GitHub 存储库](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)：</span><span class="sxs-lookup"><span data-stu-id="cf022-307">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="cf022-308">[实时站点](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="cf022-308">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="cf022-309">如果使用项目站点而非组织站点，请在 `wwwroot/index.html` 中更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="cf022-309">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="cf022-310">将 `href` 属性值设置为，包含尾部斜杠的 GitHub 存储库名称（例如，`/my-repository/`）。</span><span class="sxs-lookup"><span data-stu-id="cf022-310">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="cf022-311">在 [SteveSandersonMS/BlazorOnGitHubPages GitHub 存储库](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)中，将在发布时通过 [`.github/workflows/main.yml` 配置文件](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)更新基本 `href`。</span><span class="sxs-lookup"><span data-stu-id="cf022-311">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="cf022-312">[SteveSandersonMS/BlazorOnGitHubPages GitHub 存储库](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) 不归 .NET Foundation 或 Microsoft 所有，也不由它们提供维护和支持。</span><span class="sxs-lookup"><span data-stu-id="cf022-312">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="cf022-313">主机配置值</span><span class="sxs-lookup"><span data-stu-id="cf022-313">Host configuration values</span></span>

<span data-ttu-id="cf022-314">在开发环境中，[Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)可以在运行时接受以下主机配置值作为命令行参数。</span><span class="sxs-lookup"><span data-stu-id="cf022-314">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="cf022-315">内容根</span><span class="sxs-lookup"><span data-stu-id="cf022-315">Content root</span></span>

<span data-ttu-id="cf022-316">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径（[内容根目录](xref:fundamentals/index#content-root)）。</span><span class="sxs-lookup"><span data-stu-id="cf022-316">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="cf022-317">在下面的示例中，`/content-root-path` 是应用的内容根路径。</span><span class="sxs-lookup"><span data-stu-id="cf022-317">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="cf022-318">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="cf022-318">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cf022-319">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-319">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="cf022-320">在 IIS Express 配置文件中，向应用的 `launchSettings.json` 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="cf022-320">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="cf022-321">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="cf022-321">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="cf022-322">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="cf022-322">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cf022-323">在 Visual Studio 属性页中设置参数可将参数添加到 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-323">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="cf022-324">基路径</span><span class="sxs-lookup"><span data-stu-id="cf022-324">Path base</span></span>

<span data-ttu-id="cf022-325">`--pathbase` 参数可设置使用非根相对 URL 路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="cf022-325">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="cf022-326">在下面的示例中，`/relative-URL-path` 是应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="cf022-326">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="cf022-327">有关详细信息，请参阅[应用基路径](xref:blazor/host-and-deploy/index#app-base-path)。</span><span class="sxs-lookup"><span data-stu-id="cf022-327">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cf022-328">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="cf022-328">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="cf022-329">如果在 `<base>` 标记中以 `<base href="/CoolApp/">` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="cf022-329">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="cf022-330">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="cf022-330">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cf022-331">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-331">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="cf022-332">在 IIS Express 配置文件中，向应用的 `launchSettings.json` 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="cf022-332">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="cf022-333">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="cf022-333">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="cf022-334">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="cf022-334">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cf022-335">在 Visual Studio 属性页中设置参数可将参数添加到 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-335">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="cf022-336">URL</span><span class="sxs-lookup"><span data-stu-id="cf022-336">URLs</span></span>

<span data-ttu-id="cf022-337">`--urls` 参数设置 IP 地址或主机地址，其中包含侦听请求的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="cf022-337">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="cf022-338">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="cf022-338">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="cf022-339">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="cf022-339">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="cf022-340">在 IIS Express 配置文件中，向应用的 `launchSettings.json` 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="cf022-340">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="cf022-341">如果使用 Visual Studio 调试器并在命令提示符中运行 `dotnet run` 来运行应用，使用的是此设置。</span><span class="sxs-lookup"><span data-stu-id="cf022-341">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="cf022-342">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数  。</span><span class="sxs-lookup"><span data-stu-id="cf022-342">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="cf022-343">在 Visual Studio 属性页中设置参数可将参数添加到 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-343">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="cf022-344">配置裁边器</span><span class="sxs-lookup"><span data-stu-id="cf022-344">Configure the Trimmer</span></span>

<span data-ttu-id="cf022-345">Blazor 对每个发布版本执行中间语言 (IL) 剪裁，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="cf022-345">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="cf022-346">有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-trimmer>。</span><span class="sxs-lookup"><span data-stu-id="cf022-346">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="cf022-347">配置链接器</span><span class="sxs-lookup"><span data-stu-id="cf022-347">Configure the Linker</span></span>

<span data-ttu-id="cf022-348">Blazor 对每个发布版本执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="cf022-348">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="cf022-349">有关详细信息，请参阅 <xref:blazor/host-and-deploy/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="cf022-349">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="cf022-350">自定义启动资源加载</span><span class="sxs-lookup"><span data-stu-id="cf022-350">Custom boot resource loading</span></span>

<span data-ttu-id="cf022-351">Blazor WebAssembly 应用可使用 `loadBootResource` 函数进行初始化，以覆盖内置的启动资源加载机制。</span><span class="sxs-lookup"><span data-stu-id="cf022-351">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="cf022-352">在以下场景中使用 `loadBootResource`：</span><span class="sxs-lookup"><span data-stu-id="cf022-352">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="cf022-353">允许用户从 CDN 加载静态资源，例如时区数据或 `dotnet.wasm`。</span><span class="sxs-lookup"><span data-stu-id="cf022-353">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="cf022-354">使用 HTTP 请求加载压缩的程序集，并将其解压缩到不支持从服务器提取压缩内容的主机的客户端上。</span><span class="sxs-lookup"><span data-stu-id="cf022-354">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="cf022-355">将每个 `fetch` 请求重定向到新名称，从而为资源提供别名。</span><span class="sxs-lookup"><span data-stu-id="cf022-355">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="cf022-356">`loadBootResource` 参数如下表所示。</span><span class="sxs-lookup"><span data-stu-id="cf022-356">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="cf022-357">参数</span><span class="sxs-lookup"><span data-stu-id="cf022-357">Parameter</span></span>    | <span data-ttu-id="cf022-358">描述</span><span class="sxs-lookup"><span data-stu-id="cf022-358">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="cf022-359">资源类型。</span><span class="sxs-lookup"><span data-stu-id="cf022-359">The type of the resource.</span></span> <span data-ttu-id="cf022-360">允许使用的类型：`assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="cf022-360">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="cf022-361">资源的名称。</span><span class="sxs-lookup"><span data-stu-id="cf022-361">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="cf022-362">资源的相对或绝对 URI。</span><span class="sxs-lookup"><span data-stu-id="cf022-362">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="cf022-363">表示响应中的预期内容的完整性字符串。</span><span class="sxs-lookup"><span data-stu-id="cf022-363">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="cf022-364">`loadBootResource` 返回以下任何内容以替代加载过程：</span><span class="sxs-lookup"><span data-stu-id="cf022-364">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="cf022-365">URI 字符串。</span><span class="sxs-lookup"><span data-stu-id="cf022-365">URI string.</span></span> <span data-ttu-id="cf022-366">在以下示例 (`wwwroot/index.html`) 中，以下文件来自 CDN (`https://my-awesome-cdn.com/`)：</span><span class="sxs-lookup"><span data-stu-id="cf022-366">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="cf022-367">时区数据</span><span class="sxs-lookup"><span data-stu-id="cf022-367">Timezone data</span></span>

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

* <span data-ttu-id="cf022-368">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="cf022-368">`Promise<Response>`.</span></span> <span data-ttu-id="cf022-369">在标头中传递 `integrity` 参数以保持默认的完整性检查行为。</span><span class="sxs-lookup"><span data-stu-id="cf022-369">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="cf022-370">以下示例 (`wwwroot/index.html`) 将一个自定义 HTTP 标头添加到出站请求，并将 `integrity` 参数传递到 `fetch` 调用：</span><span class="sxs-lookup"><span data-stu-id="cf022-370">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="cf022-371">`null`/`undefined`，这将导致默认的加载行为。</span><span class="sxs-lookup"><span data-stu-id="cf022-371">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="cf022-372">外部源必须返回浏览器所需的 CORS 标头以允许跨域资源加载。</span><span class="sxs-lookup"><span data-stu-id="cf022-372">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="cf022-373">CDN 通常会默认提供所需的标头。</span><span class="sxs-lookup"><span data-stu-id="cf022-373">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="cf022-374">你只需指定自定义行为的类型。</span><span class="sxs-lookup"><span data-stu-id="cf022-374">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="cf022-375">框架会根据默认的加载行为加载未指定到 `loadBootResource` 的类型。</span><span class="sxs-lookup"><span data-stu-id="cf022-375">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="cf022-376">更改 DLL 文件的文件扩展名</span><span class="sxs-lookup"><span data-stu-id="cf022-376">Change the filename extension of DLL files</span></span>

<span data-ttu-id="cf022-377">如果需要更改应用的已发布 `.dll` 文件的文件扩展名，请按照本部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="cf022-377">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="cf022-378">发布应用后，使用 shell 脚本或 DevOps 生成管道将 `.dll` 文件重命名，以使用其他文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="cf022-378">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="cf022-379">将 `.dll` 文件的目标位置设为应用的已发布输出的 `wwwroot` 目录中（例如 `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`）。</span><span class="sxs-lookup"><span data-stu-id="cf022-379">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="cf022-380">在下面的示例中，重命名 `.dll` 文件，以使用 `.bin` 文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="cf022-380">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="cf022-381">在 Windows 上：</span><span class="sxs-lookup"><span data-stu-id="cf022-381">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="cf022-382">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="cf022-382">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="cf022-383">在 Linux 或 macOS 上：</span><span class="sxs-lookup"><span data-stu-id="cf022-383">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="cf022-384">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="cf022-384">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="cf022-385">要使用不同于 `.bin` 的其他文件扩展名，请在前面的命令中替换 `.bin`。</span><span class="sxs-lookup"><span data-stu-id="cf022-385">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="cf022-386">若要处理压缩的 `blazor.boot.json.gz` 和 `blazor.boot.json.br` 文件，请采用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="cf022-386">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="cf022-387">删除压缩的 `blazor.boot.json.gz` 和 `blazor.boot.json.br` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-387">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="cf022-388">此方法禁用压缩。</span><span class="sxs-lookup"><span data-stu-id="cf022-388">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="cf022-389">重新压缩更新后的 `blazor.boot.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="cf022-389">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="cf022-390">上述指导也适用于正在使用服务工作进程资产的情况。</span><span class="sxs-lookup"><span data-stu-id="cf022-390">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="cf022-391">删除或重新压缩 `wwwroot/service-worker-assets.js.br` 和 `wwwroot/service-worker-assets.js.gz`。</span><span class="sxs-lookup"><span data-stu-id="cf022-391">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="cf022-392">否则，浏览器中的文件完整性检查将失败。</span><span class="sxs-lookup"><span data-stu-id="cf022-392">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="cf022-393">以下 Windows 示例使用项目根目录中的 PowerShell 脚本。</span><span class="sxs-lookup"><span data-stu-id="cf022-393">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="cf022-394">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="cf022-394">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="cf022-395">如果服务工作进程资产也在使用中，请添加以下命令：</span><span class="sxs-lookup"><span data-stu-id="cf022-395">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="cf022-396">在项目文件中，在发布应用后运行脚本：</span><span class="sxs-lookup"><span data-stu-id="cf022-396">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="cf022-397">重命名和延迟加载相同的程序集时，请参阅 <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> 中的指南。</span><span class="sxs-lookup"><span data-stu-id="cf022-397">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

<span data-ttu-id="cf022-398">若要提供反馈，请访问 [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477)。</span><span class="sxs-lookup"><span data-stu-id="cf022-398">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
