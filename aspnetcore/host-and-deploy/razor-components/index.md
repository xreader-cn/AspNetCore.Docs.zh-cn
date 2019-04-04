---
title: 托管和部署 Razor 组件
author: guardrex
description: 了解如何使用 ASP.NET Core、内容分发网络 (CDN)、文件服务器以及 GitHub 页托管和部署 Razor 组件和 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/22/2019
uid: host-and-deploy/razor-components/index
ms.openlocfilehash: 236e8da27b80dbdb3e0ea885413b6cfd563dde60
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419415"
---
# <a name="host-and-deploy-razor-components"></a><span data-ttu-id="2f900-103">托管和部署 Razor 组件</span><span class="sxs-lookup"><span data-stu-id="2f900-103">Host and deploy Razor Components</span></span>

<span data-ttu-id="2f900-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2f900-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="2f900-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="2f900-105">Publish the app</span></span>

<span data-ttu-id="2f900-106">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="2f900-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="2f900-107">可处理执行 `dotnet publish` 命令的 IDE 将自动使用其内置发布功能，因此可能无需通过命令提示符手动执行该命令，但具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="2f900-107">An IDE may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="2f900-108">创建部署资产前，`dotnet publish` 将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)该项目。</span><span class="sxs-lookup"><span data-stu-id="2f900-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="2f900-109">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="2f900-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="2f900-110">部署创建于 /bin/Release/&lt;target-framework&gt;/publish 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2f900-110">The deployment is created in the */bin/Release/&lt;target-framework&gt;/publish* folder.</span></span>

<span data-ttu-id="2f900-111">“publish”文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="2f900-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="2f900-112">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="2f900-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="2f900-113">主机配置值</span><span class="sxs-lookup"><span data-stu-id="2f900-113">Host configuration values</span></span>

<span data-ttu-id="2f900-114">使用[服务器端托管模型](xref:razor-components/hosting-models#server-side-hosting-model)的 Razor 组件可以接受 [Web 主机配置值](xref:fundamentals/host/web-host#host-configuration-values)。</span><span class="sxs-lookup"><span data-stu-id="2f900-114">Razor Components apps that use the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model) can accept [Web Host configuration values](xref:fundamentals/host/web-host#host-configuration-values).</span></span>

<span data-ttu-id="2f900-115">使用[客户端托管模型](xref:razor-components/hosting-models#client-side-hosting-model)的 Blazor 应用可以接受以下主机配置值，作为开发环境中运行时的命令行参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-115">Blazor apps that use the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="2f900-116">内容根</span><span class="sxs-lookup"><span data-stu-id="2f900-116">Content Root</span></span>

<span data-ttu-id="2f900-117">`--contentroot` 参数设置包含应用内容文件的目录的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="2f900-117">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span>

* <span data-ttu-id="2f900-118">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-118">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2f900-119">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2f900-119">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* <span data-ttu-id="2f900-120">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="2f900-120">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2f900-121">使用 Visual Studio 调试程序运行应用和使用 `dotnet run` 从命令提示符处运行应用时，选择此设置。</span><span class="sxs-lookup"><span data-stu-id="2f900-121">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* <span data-ttu-id="2f900-122">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-122">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2f900-123">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2f900-123">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a><span data-ttu-id="2f900-124">基路径</span><span class="sxs-lookup"><span data-stu-id="2f900-124">Path base</span></span>

<span data-ttu-id="2f900-125">`--pathbase` 参数可设置使用非根虚拟路径本地运行的应用的应用基路径（将 `<base>` 标记 `href` 针对暂存和生产设置为 `/` 之外的路径）。</span><span class="sxs-lookup"><span data-stu-id="2f900-125">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="2f900-126">有关详细信息，请参阅[应用基路径](#app-base-path)部分。</span><span class="sxs-lookup"><span data-stu-id="2f900-126">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2f900-127">不同于向 `href` 标记的 `<base>` 提供的路径，传递 `--pathbase` 参数值时不包括尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="2f900-127">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="2f900-128">如果在 `<base>` 标记中以 `<base href="/CoolApp/" />` 形式（包括尾部反斜杠）提供应用基路径，则以 `--pathbase=/CoolApp` 形式（无尾部反斜杠）传递命令行参数值。</span><span class="sxs-lookup"><span data-stu-id="2f900-128">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/" />` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="2f900-129">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-129">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2f900-130">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2f900-130">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* <span data-ttu-id="2f900-131">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="2f900-131">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2f900-132">使用 Visual Studio 调试程序运行应用和使用 `dotnet run` 从命令提示符处运行应用时，选择此设置。</span><span class="sxs-lookup"><span data-stu-id="2f900-132">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* <span data-ttu-id="2f900-133">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-133">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2f900-134">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2f900-134">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a><span data-ttu-id="2f900-135">URL</span><span class="sxs-lookup"><span data-stu-id="2f900-135">URLs</span></span>

<span data-ttu-id="2f900-136">`--urls` 参数指示 IP 地址或主机地址，其中包含针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="2f900-136">The `--urls` argument indicates the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="2f900-137">以本地方式在命令提示符下运行应用时传递该参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-137">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="2f900-138">在应用的目录中，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2f900-138">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="2f900-139">在 IIS Express 配置文件中，向应用的 launchSettings.json 文件添加条目。</span><span class="sxs-lookup"><span data-stu-id="2f900-139">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="2f900-140">使用 Visual Studio 调试程序运行应用和使用 `dotnet run` 从命令提示符处运行应用时，选择此设置。</span><span class="sxs-lookup"><span data-stu-id="2f900-140">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="2f900-141">在 Visual Studio 中，在“属性” > “调试” > “应用程序参数”中指定参数。</span><span class="sxs-lookup"><span data-stu-id="2f900-141">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="2f900-142">在 Visual Studio 属性页中设置参数可将参数添加到 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="2f900-142">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a><span data-ttu-id="2f900-143">部署客户端 Blazor 应用</span><span class="sxs-lookup"><span data-stu-id="2f900-143">Deploy a client-side Blazor app</span></span>

<span data-ttu-id="2f900-144">使用[客户端托管模型](xref:razor-components/hosting-models#client-side-hosting-model)：</span><span class="sxs-lookup"><span data-stu-id="2f900-144">With the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="2f900-145">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="2f900-145">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="2f900-146">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="2f900-146">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="2f900-147">支持以下策略之一：</span><span class="sxs-lookup"><span data-stu-id="2f900-147">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="2f900-148">Blazor 应用由 ASP.NET Core 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-148">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="2f900-149">请参阅[使用 ASP.NET Core 的客户端 Blazor 托管部署](#client-side-blazor-hosted-deployment-with-aspnet-core)部分。</span><span class="sxs-lookup"><span data-stu-id="2f900-149">Covered in the [Client-side Blazor hosted deployment with ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="2f900-150">Blazor 应用位于静态托管 Web 服务器或服务中，其中未使用 .NET 对 Blazor 应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-150">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="2f900-151">请参阅[客户端 Blazor 独立部署](#client-side-blazor-standalone-deployment)部分。</span><span class="sxs-lookup"><span data-stu-id="2f900-151">Covered in the [Client-side Blazor standalone deployment](#client-side-blazor-standalone-deployment) section.</span></span>

### <a name="configure-the-linker"></a><span data-ttu-id="2f900-152">配置链接器</span><span class="sxs-lookup"><span data-stu-id="2f900-152">Configure the Linker</span></span>

<span data-ttu-id="2f900-153">Blazor 对每个生成执行中间语言 (IL) 链接，以从输出程序集中删除不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="2f900-153">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="2f900-154">可以控制生成的程序集链接。</span><span class="sxs-lookup"><span data-stu-id="2f900-154">You can control assembly linking on build.</span></span> <span data-ttu-id="2f900-155">有关更多信息，请参见<xref:host-and-deploy/razor-components/configure-linker>。</span><span class="sxs-lookup"><span data-stu-id="2f900-155">For more information, see <xref:host-and-deploy/razor-components/configure-linker>.</span></span>

### <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="2f900-156">重写 URL，以实现正确路由</span><span class="sxs-lookup"><span data-stu-id="2f900-156">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="2f900-157">客户端应用中对页组件的路由请求不如服务器端对托管应用的路由请求简单。</span><span class="sxs-lookup"><span data-stu-id="2f900-157">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="2f900-158">请考虑具有两个页面的客户端应用：</span><span class="sxs-lookup"><span data-stu-id="2f900-158">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="2f900-159">Main.cshtml &ndash; 在应用的根目录下加载，且包含“关于”页链接 (`href="About"`)。</span><span class="sxs-lookup"><span data-stu-id="2f900-159">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="2f900-160">About.cshtml &ndash;“关于”页。</span><span class="sxs-lookup"><span data-stu-id="2f900-160">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="2f900-161">使用浏览器的地址栏（例如，`https://www.contoso.com/`）请求应用的默认文档：</span><span class="sxs-lookup"><span data-stu-id="2f900-161">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="2f900-162">浏览器发出请求。</span><span class="sxs-lookup"><span data-stu-id="2f900-162">The browser makes a request.</span></span>
1. <span data-ttu-id="2f900-163">返回默认页，通常为 index.html。</span><span class="sxs-lookup"><span data-stu-id="2f900-163">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="2f900-164">index.html 启动应用。</span><span class="sxs-lookup"><span data-stu-id="2f900-164">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="2f900-165">Blazor 的路由器将加载，并且将显示 Razor Main 页 (Main.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="2f900-165">Blazor's router loads and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="2f900-166">在主页上，选择“关于”页面的链接可加载“关于”页面。</span><span class="sxs-lookup"><span data-stu-id="2f900-166">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="2f900-167">选择“关于”页面的链接适用于客户端，因为 Blazor 路由器阻止浏览器对 Internet 发出请求，针对 `About` 转到 `www.contoso.com`，并为“关于”页本身提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-167">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="2f900-168">针对客户端应用中的内部页的所有请求，工作原理都相同：这些请求不会触发对 Internet 上的服务器托管资源的基于浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="2f900-168">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="2f900-169">路由器将在内部处理请求。</span><span class="sxs-lookup"><span data-stu-id="2f900-169">The router handles the requests internally.</span></span>

<span data-ttu-id="2f900-170">如果针对 `www.contoso.com/About` 使用浏览器的地址栏发出请求，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="2f900-170">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="2f900-171">应用的 Internet 主机上不存在此类资源，因此将返回“404 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="2f900-171">No such resource exists on the app's Internet host, so a *404 Not found* response is returned.</span></span>

<span data-ttu-id="2f900-172">由于浏览器针对客户端页面请求基于 Internet 的主机，因此 Web 服务器和托管服务必须将对服务器上非物理方式资源的所有请求重写为 index.html 页。</span><span class="sxs-lookup"><span data-stu-id="2f900-172">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="2f900-173">如果返回 index.html，应用的客户端路由器将接管工作并使用正确的资源响应。</span><span class="sxs-lookup"><span data-stu-id="2f900-173">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

### <a name="app-base-path"></a><span data-ttu-id="2f900-174">应用基路径</span><span class="sxs-lookup"><span data-stu-id="2f900-174">App base path</span></span>

<span data-ttu-id="2f900-175">应用基路径是服务器上的虚拟应用根路径。</span><span class="sxs-lookup"><span data-stu-id="2f900-175">The app base path is the virtual app root path on the server.</span></span> <span data-ttu-id="2f900-176">例如，位于 `/CoolApp/` 虚拟文件夹中 Contoso 服务器上的应用可通过 `https://www.contoso.com/CoolApp` 进行访问，并且该应用的虚拟基路径为 `/CoolApp/`。</span><span class="sxs-lookup"><span data-stu-id="2f900-176">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="2f900-177">通过将应用基路径设置为 `CoolApp/`，应用就会知道其在服务器上实际所在的位置。</span><span class="sxs-lookup"><span data-stu-id="2f900-177">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="2f900-178">应用可使用应用基路径通过不在根目录中的组件构造应用根目录的相对 URL。</span><span class="sxs-lookup"><span data-stu-id="2f900-178">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="2f900-179">通过这种方式，位于目录结构不同级别的组件可生成指向整个应用其他位置的资源链接。</span><span class="sxs-lookup"><span data-stu-id="2f900-179">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="2f900-180">应用基路径也用于截获超链接单击，其中链接的 `href` 目标位于应用基路径 URI 空间中 &mdash; Blazor 路由器可处理内部导航。</span><span class="sxs-lookup"><span data-stu-id="2f900-180">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="2f900-181">在许多托管方案中，应用的服务器虚拟路径为应用的根目录。</span><span class="sxs-lookup"><span data-stu-id="2f900-181">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="2f900-182">在这些情况下，应用基路径为正斜杠 (`<base href="/" />`)，它是应用的默认配置。</span><span class="sxs-lookup"><span data-stu-id="2f900-182">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="2f900-183">在其他托管方案中，例如 GitHub 页和 IIS 虚拟目录或子应用程序，应用基路径必须设置为应用的服务器虚拟路径。</span><span class="sxs-lookup"><span data-stu-id="2f900-183">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="2f900-184">要设置应用的基路径，请在 `<head>` 标记元素中找到的 index.html 中添加或更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="2f900-184">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="2f900-185">将 `href` 属性值设置为 `<virtual-path>/`（需要尾部反斜杠），其中 `<virtual-path>/` 是应用的服务器上的完整虚拟应用根路径。</span><span class="sxs-lookup"><span data-stu-id="2f900-185">Set the `href` attribute value to `<virtual-path>/` (the trailing slash is required), where `<virtual-path>/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="2f900-186">在上述示例中，虚拟路径设置为 `CoolApp/`：`<base href="CoolApp/" />`。</span><span class="sxs-lookup"><span data-stu-id="2f900-186">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/" />`.</span></span>

<span data-ttu-id="2f900-187">对于配置了非根虚拟路径的应用（例如，`<base href="CoolApp/" />`），当在本地运行应用时，将无法查找其资源。</span><span class="sxs-lookup"><span data-stu-id="2f900-187">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/" />`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="2f900-188">要在本地开发和测试过程中解决此问题，可提供 path base 参数，用于匹配运行时 `<base>` 标记的 `href` 值。</span><span class="sxs-lookup"><span data-stu-id="2f900-188">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="2f900-189">要在本地运行应用时传递带根路径 (`/`) 的 path base 参数，请在应用的目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2f900-189">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="2f900-190">应用在 `http://localhost:port/CoolApp` 本地响应。</span><span class="sxs-lookup"><span data-stu-id="2f900-190">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="2f900-191">有关详细信息，请参阅[基路径主机配置值](#path-base)部分。</span><span class="sxs-lookup"><span data-stu-id="2f900-191">For more information, see the [path base host configuration value](#path-base) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2f900-192">如果应用使用[客户端托管模型](xref:razor-components/hosting-models#client-side-hosting-model)（基于 Blazor 项目模板），并且作为 ASP.NET Core 应用中的 IIS 子应用程序进行托管，则有必要禁用已继承的 ASP.NET Core 模块处理程序或确保根应用的 web.config 文件中的 `<handlers>` 部分未由子应用继承。</span><span class="sxs-lookup"><span data-stu-id="2f900-192">If an app uses the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>
>
> <span data-ttu-id="2f900-193">通过向文件添加 `<handlers>` 部分，删除应用已发布 web.config 文件中的处理程序：</span><span class="sxs-lookup"><span data-stu-id="2f900-193">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> <span data-ttu-id="2f900-194">或者，通过使用 `<location>` 元素并且将 `inheritInChildApplications` 设置为 `false`，禁止继承根（父级）应用的 `<system.webServer>` 部分：</span><span class="sxs-lookup"><span data-stu-id="2f900-194">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> <span data-ttu-id="2f900-195">如本部分中所述，除配置应用的基路径外，还需删除处理程序或禁用继承。</span><span class="sxs-lookup"><span data-stu-id="2f900-195">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="2f900-196">在 IIS 中配置子应用时，在应用的 index.html 文件中将应用基路径设置为 IIS 别名。</span><span class="sxs-lookup"><span data-stu-id="2f900-196">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a><span data-ttu-id="2f900-197">使用 ASP.NET Core 的客户端 Blazor 托管部署</span><span class="sxs-lookup"><span data-stu-id="2f900-197">Client-side Blazor hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="2f900-198">托管部署通过在服务器上运行的 [ASP.NET Core 应用](xref:index)为浏览器提供客户端 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="2f900-198">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="2f900-199">Blazor 应用随附于已发布输出中的 ASP.NET Core 应用，因此这两个应用将一起进行部署。</span><span class="sxs-lookup"><span data-stu-id="2f900-199">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="2f900-200">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="2f900-200">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="2f900-201">对于托管部署，Visual Studio 包括 Blazor（托管 ASP.NET Core）项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `blazorhosted` 模板）。</span><span class="sxs-lookup"><span data-stu-id="2f900-201">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="2f900-202">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="2f900-202">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2f900-203">有关如何部署到 Azure 应用服务的信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="2f900-203">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>

<span data-ttu-id="2f900-204">了解如何使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-204">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

### <a name="client-side-blazor-standalone-deployment"></a><span data-ttu-id="2f900-205">客户端 Blazor 独立部署</span><span class="sxs-lookup"><span data-stu-id="2f900-205">Client-side Blazor standalone deployment</span></span>

<span data-ttu-id="2f900-206">独立部署将客户端 Blazor 应用作为客户端直接请求的一组静态文件。</span><span class="sxs-lookup"><span data-stu-id="2f900-206">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="2f900-207">Web 服务器不用于对 Blazor 提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-207">A web server isn't used to serve the Blazor app.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a><span data-ttu-id="2f900-208">使用 IIS 的客户端 Blazor 独立托管</span><span class="sxs-lookup"><span data-stu-id="2f900-208">Client-side Blazor standalone hosting with IIS</span></span>

<span data-ttu-id="2f900-209">IIS 是适用于 Blazor 应用的强大静态文件服务器。</span><span class="sxs-lookup"><span data-stu-id="2f900-209">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="2f900-210">要配置 IIS 以托管 Blazor，请参阅[在 IIS 上生成静态网站](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="2f900-210">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="2f900-211">已发布资产创建于 \\bin\\Release\\&lt;target-framework&gt;\\publish 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2f900-211">Published assets are created in the *\\bin\\Release\\&lt;target-framework&gt;\\publish* folder.</span></span> <span data-ttu-id="2f900-212">在 Web 服务器或托管服务上托管“publish”文件夹内容。</span><span class="sxs-lookup"><span data-stu-id="2f900-212">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

<span data-ttu-id="2f900-213">**web.config**</span><span class="sxs-lookup"><span data-stu-id="2f900-213">**web.config**</span></span>

<span data-ttu-id="2f900-214">发布 Blazor 项目时，将使用以下 IIS 配置创建 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="2f900-214">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="2f900-215">对以下文件扩展名设置 MIME 类型：</span><span class="sxs-lookup"><span data-stu-id="2f900-215">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="2f900-216">\*.dll：`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="2f900-216">*\*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="2f900-217">\*.json：`application/json`</span><span class="sxs-lookup"><span data-stu-id="2f900-217">*\*.json*: `application/json`</span></span>
  * <span data-ttu-id="2f900-218">\*.wasm：`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="2f900-218">*\*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="2f900-219">\*.woff：`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2f900-219">*\*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="2f900-220">\*.woff2：`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="2f900-220">*\*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="2f900-221">对以下 MIME 类型启用 HTTP 压缩：</span><span class="sxs-lookup"><span data-stu-id="2f900-221">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="2f900-222">建立 URL 重写模块规则：</span><span class="sxs-lookup"><span data-stu-id="2f900-222">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="2f900-223">为应用的静态资产所在的子目录 (&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;) 提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-223">Serve the sub-directory where the app's static assets reside (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).</span></span>
  * <span data-ttu-id="2f900-224">创建 SPA 回退路由，以便将对非文件资产的请求重定向到其静态资产文件夹中的应用默认文档 (&lt;assembly_name&gt;\\dist\\index.html)。</span><span class="sxs-lookup"><span data-stu-id="2f900-224">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*&lt;assembly_name&gt;\\dist\\index.html*).</span></span>

<span data-ttu-id="2f900-225">**安装 URL 重写模块**</span><span class="sxs-lookup"><span data-stu-id="2f900-225">**Install the URL Rewrite Module**</span></span>

<span data-ttu-id="2f900-226">重写 URL 必须使用 [URL 重写模块](https://www.iis.net/downloads/microsoft/url-rewrite)。</span><span class="sxs-lookup"><span data-stu-id="2f900-226">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="2f900-227">默认不安装该模块，并且它不可作为 Web 服务器 (IIS) 角色服务功能进行安装。</span><span class="sxs-lookup"><span data-stu-id="2f900-227">The module isn't installed by default, and it isn't available for install as an Web Server (IIS) role service feature.</span></span> <span data-ttu-id="2f900-228">必须从 IIS 网站下载该模块。</span><span class="sxs-lookup"><span data-stu-id="2f900-228">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="2f900-229">使用 Web 平台安装程序安装模块：</span><span class="sxs-lookup"><span data-stu-id="2f900-229">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="2f900-230">以本地方式导航到 [URL 重写模块下载页](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)。</span><span class="sxs-lookup"><span data-stu-id="2f900-230">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="2f900-231">对于英语版本，请选择“WebPI”以下载 WebPI 安装程序。</span><span class="sxs-lookup"><span data-stu-id="2f900-231">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="2f900-232">对于其他语言，请选择适当的服务器体系结构 (x86/x64) 下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="2f900-232">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="2f900-233">将安装程序复制到服务器。</span><span class="sxs-lookup"><span data-stu-id="2f900-233">Copy the installer to the server.</span></span> <span data-ttu-id="2f900-234">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="2f900-234">Run the installer.</span></span> <span data-ttu-id="2f900-235">选择“安装”按钮，并接受许可条款。</span><span class="sxs-lookup"><span data-stu-id="2f900-235">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="2f900-236">安装完成后无需重启服务器。</span><span class="sxs-lookup"><span data-stu-id="2f900-236">A server restart isn't required after the install completes.</span></span>

<span data-ttu-id="2f900-237">**配置网站**</span><span class="sxs-lookup"><span data-stu-id="2f900-237">**Configure the website**</span></span>

<span data-ttu-id="2f900-238">将网站的物理路径设置为应用的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2f900-238">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="2f900-239">该文件夹包含：</span><span class="sxs-lookup"><span data-stu-id="2f900-239">The folder contains:</span></span>

* <span data-ttu-id="2f900-240">web.config 文件，IIS 使用该文件配置网站，包括所需的重定向规则和文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="2f900-240">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="2f900-241">应用的静态资产文件夹。</span><span class="sxs-lookup"><span data-stu-id="2f900-241">The app's static asset folder.</span></span>

<span data-ttu-id="2f900-242">**疑难解答**</span><span class="sxs-lookup"><span data-stu-id="2f900-242">**Troubleshooting**</span></span>

<span data-ttu-id="2f900-243">如果收到“500 内部服务器错误”且在尝试访问网站配置时 IIS 管理器引发错误，请确认已安装 URL 重写模块。</span><span class="sxs-lookup"><span data-stu-id="2f900-243">If a *500 Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="2f900-244">如果未安装该模块，则 IIS 无法分析 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="2f900-244">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="2f900-245">这可以防止 IIS 管理器加载网站配置，并防止网站对 Blazor 的静态文件提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-245">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="2f900-246">有关排查部署到 IIS 的问题的详细信息，请参阅 <xref:host-and-deploy/iis/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="2f900-246">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a><span data-ttu-id="2f900-247">使用 Nginx 的客户端 Blazor 独立托管</span><span class="sxs-lookup"><span data-stu-id="2f900-247">Client-side Blazor standalone hosting with Nginx</span></span>

<span data-ttu-id="2f900-248">以下 nginx.conf 文件经过简化，以显示如何配置 Nginx，以在每当无法在磁盘上找到对应的文件时发送 Index.html 文件。</span><span class="sxs-lookup"><span data-stu-id="2f900-248">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *Index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

<span data-ttu-id="2f900-249">有关生产 Nginx Web 服务器配置的详细信息，请参阅 [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)（创建 NGINX 增强版和 NGINX 配置文件）。</span><span class="sxs-lookup"><span data-stu-id="2f900-249">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a><span data-ttu-id="2f900-250">Docker 中使用 Nginx 的客户端 Blazor 独立托管</span><span class="sxs-lookup"><span data-stu-id="2f900-250">Client-side Blazor standalone hosting with Nginx in Docker</span></span>

<span data-ttu-id="2f900-251">要使用 Nginx 在 Docker 中托管 Blazor，请设置 Dockerfile，以使用基于 Alpine 的 Nginx 映像。</span><span class="sxs-lookup"><span data-stu-id="2f900-251">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="2f900-252">更新 Dockerfile，以将 nginx.config 文件复制到容器。</span><span class="sxs-lookup"><span data-stu-id="2f900-252">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="2f900-253">向 Dockerfile 添加一行，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="2f900-253">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a><span data-ttu-id="2f900-254">使用 GitHub 页的客户端 Blazor 独立托管</span><span class="sxs-lookup"><span data-stu-id="2f900-254">Client-side Blazor standalone hosting with GitHub Pages</span></span>

<span data-ttu-id="2f900-255">要处理 URL 重写，请使用脚本添加 404.html 文件，该脚本可处理到 index.html 页的重定向请求。</span><span class="sxs-lookup"><span data-stu-id="2f900-255">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="2f900-256">关于社区提供的示例实现，请参阅[用于 GitHub 页的单页应用](http://spa-github-pages.rafrex.com/)（[GitHub 上的 rafrex/spa-github-pages](https://github.com/rafrex/spa-github-pages#readme)）。</span><span class="sxs-lookup"><span data-stu-id="2f900-256">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="2f900-257">可在 [GitHub 上的 blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io)（[实时站点](https://blazor-demo.github.io/)）查看使用社区方法的示例。</span><span class="sxs-lookup"><span data-stu-id="2f900-257">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="2f900-258">如果使用项目站点而非组织站点，请在 index.html 中添加或更新 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="2f900-258">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="2f900-259">将 `href` 属性值设置为 `<repository-name>/`，其中 `<repository-name>/` 为 GitHub 存储库名称。</span><span class="sxs-lookup"><span data-stu-id="2f900-259">Set the `href` attribute value to `<repository-name>/`, where `<repository-name>/` is the GitHub repository name.</span></span>

## <a name="deploy-a-server-side-razor-components-app"></a><span data-ttu-id="2f900-260">部署服务器端 Razor 组件应用</span><span class="sxs-lookup"><span data-stu-id="2f900-260">Deploy a server-side Razor Components app</span></span>

<span data-ttu-id="2f900-261">使用[服务器端托管模型](xref:razor-components/hosting-models#server-side-hosting-model)，Razor 组件在 ASP.NET Core 应用中的服务器上执行。</span><span class="sxs-lookup"><span data-stu-id="2f900-261">With the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model), Razor Components is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="2f900-262">通过 SignalR 连接处理 UI 更新、事件处理和 JavaScript 调用。</span><span class="sxs-lookup"><span data-stu-id="2f900-262">UI updates, event handling, and JavaScript calls are handled over a SignalR connection.</span></span>

<span data-ttu-id="2f900-263">该应用随附于已发布输出中的 ASP.NET Core 应用，因此这两个应用一起进行部署。</span><span class="sxs-lookup"><span data-stu-id="2f900-263">The app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="2f900-264">需要能够托管 ASP.NET Core 应用的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="2f900-264">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="2f900-265">对于服务器端部署，Visual Studio 包括“Blazor 组件”项目模板（使用 [dotnet new](/dotnet/core/tools/dotnet-new) 命令时为 `razorcomponents` 模板）。</span><span class="sxs-lookup"><span data-stu-id="2f900-265">For a server-side deployment, Visual Studio includes the **Razor Components** project template (`razorcomponents` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="2f900-266">发布 ASP.NET Core 应用后，Razor 组件应用包含在已发布输出中，因此可以一起部署 ASP.NET Core 应用和 Razor 组件应用。</span><span class="sxs-lookup"><span data-stu-id="2f900-266">When the ASP.NET Core app is published, the Razor Components app is included in the published output so that the ASP.NET Core app and the Razor Components app can be deployed together.</span></span> <span data-ttu-id="2f900-267">有关托管和部署 ASP.NET Core 应用的详细信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="2f900-267">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="2f900-268">有关如何部署到 Azure 应用服务的信息，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="2f900-268">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>

<span data-ttu-id="2f900-269">了解如何使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="2f900-269">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>
