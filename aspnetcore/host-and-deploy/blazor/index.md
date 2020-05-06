---
title: 托管和部署 ASP.NET Core Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 9d57b81cd813d02a65b6d3a39c7f1a1aa8a069c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775162"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="140fd-103">托管和部署 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="140fd-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="140fd-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="140fd-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="140fd-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="140fd-105">Publish the app</span></span>

<span data-ttu-id="140fd-106">发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="140fd-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="140fd-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="140fd-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="140fd-108">从导航栏中选择“生成”   > “发布{应用程序}”  。</span><span class="sxs-lookup"><span data-stu-id="140fd-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="140fd-109">选择“发布目标”  。</span><span class="sxs-lookup"><span data-stu-id="140fd-109">Select the *publish target*.</span></span> <span data-ttu-id="140fd-110">若要在本地发布，请选择“文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="140fd-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="140fd-111">接受“选择文件夹”  字段中的默认位置，或指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="140fd-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="140fd-112">选择“发布”  按钮。</span><span class="sxs-lookup"><span data-stu-id="140fd-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="140fd-113">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="140fd-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="140fd-114">选择“生成” > “发布到文件夹”   。</span><span class="sxs-lookup"><span data-stu-id="140fd-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="140fd-115">确认用于接收发布的资产的文件夹，并选择“发布”  。</span><span class="sxs-lookup"><span data-stu-id="140fd-115">Confirm the folder to receive the published assets and select **Publish**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="140fd-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="140fd-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="140fd-117">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布具有发布配置的应用：</span><span class="sxs-lookup"><span data-stu-id="140fd-117">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="140fd-118">创建部署资产前，发布应用将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)生成该项目。</span><span class="sxs-lookup"><span data-stu-id="140fd-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="140fd-119">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="140fd-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="140fd-120">发布位置：</span><span class="sxs-lookup"><span data-stu-id="140fd-120">Publish locations:</span></span>

* Blazor<span data-ttu-id="140fd-121"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="140fd-121"> WebAssembly</span></span>
  * <span data-ttu-id="140fd-122">独立 &ndash; 应用将发布到 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="140fd-122">Standalone &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span> <span data-ttu-id="140fd-123">若要将应用部署为静态站点，请将 wwwroot 文件夹的内容复制到静态站点主机  。</span><span class="sxs-lookup"><span data-stu-id="140fd-123">To deploy the app as a static site, copy the contents of the *wwwroot* folder to the static site host.</span></span>
  * <span data-ttu-id="140fd-124">托管 &ndash; 客户端 Blazor WebAssembly 应用将发布到服务器应用的 /bin/Release/{TARGET FRAMEWORK}/publish/wwwroot 文件夹，以及服务器应用的任何其他静态 Web 资产  。</span><span class="sxs-lookup"><span data-stu-id="140fd-124">Hosted &ndash; The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="140fd-125">将 publish 文件夹的内容部署到主机  。</span><span class="sxs-lookup"><span data-stu-id="140fd-125">Deploy the contents of the *publish* folder to the host.</span></span>
* Blazor<span data-ttu-id="140fd-126"> 服务器 &ndash; 应用将发布到 /bin/Release/{TARGET FRAMEWORK}/publish 文件夹  。</span><span class="sxs-lookup"><span data-stu-id="140fd-126"> Server &ndash; The app is published into the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="140fd-127">将 publish 文件夹的内容部署到主机  。</span><span class="sxs-lookup"><span data-stu-id="140fd-127">Deploy the contents of the *publish* folder to the host.</span></span>

<span data-ttu-id="140fd-128">文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="140fd-128">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="140fd-129">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="140fd-129">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="140fd-130">应用基路径</span><span class="sxs-lookup"><span data-stu-id="140fd-130">App base path</span></span>

<span data-ttu-id="140fd-131">应用基路径是应用的根 URL 路径。 </span><span class="sxs-lookup"><span data-stu-id="140fd-131">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="140fd-132">请考虑使用下列 ASP.NET Core 应用和 Blazor 子应用：</span><span class="sxs-lookup"><span data-stu-id="140fd-132">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="140fd-133">ASP.NET Core 应用名为 `MyApp`：</span><span class="sxs-lookup"><span data-stu-id="140fd-133">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="140fd-134">该应用实际驻留在 d:/MyApp 中  。</span><span class="sxs-lookup"><span data-stu-id="140fd-134">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="140fd-135">在 `https://www.contoso.com/{MYAPP RESOURCE}` 收到请求。</span><span class="sxs-lookup"><span data-stu-id="140fd-135">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="140fd-136">名为 `CoolApp` 的 Blazor 应用是 `MyApp` 的子应用：</span><span class="sxs-lookup"><span data-stu-id="140fd-136">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="140fd-137">子应用实际驻留在 d:/MyApp/CoolApp 中  。</span><span class="sxs-lookup"><span data-stu-id="140fd-137">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="140fd-138">在 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` 收到请求。</span><span class="sxs-lookup"><span data-stu-id="140fd-138">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="140fd-139">如果不为 `CoolApp` 指定其他配置，此方案中的子应用将不知道其在服务器上的位置。</span><span class="sxs-lookup"><span data-stu-id="140fd-139">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="140fd-140">例如，不知道它驻留在相对 URL 路径 `/CoolApp/` 上，应用就无法构造其资源的正确相对 URL。</span><span class="sxs-lookup"><span data-stu-id="140fd-140">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="140fd-141">要为 Blazor 应用的基路径 `https://www.contoso.com/CoolApp/` 提供配置，请将 `<base>` 标记的 `href` 属性设置为 Pages/_Host.cshtml 文件（Blazor 服务器）或 wwwroot/index.html 文件 (Blazor WebAssembly) 中的相对根路径   ：</span><span class="sxs-lookup"><span data-stu-id="140fd-141">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="140fd-142"> 服务器应用还会在应用的请求管道 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>，设置服务器端基路径：</span><span class="sxs-lookup"><span data-stu-id="140fd-142"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="140fd-143">提供相对 URL 路径，不再根目录中的元件可以构造相对于应用根路径的 URL。</span><span class="sxs-lookup"><span data-stu-id="140fd-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="140fd-144">位于目录结构不同级别的组件可生成指向整个应用其他位置的资源链接。</span><span class="sxs-lookup"><span data-stu-id="140fd-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="140fd-145">应用基路径也用于拦截所选超链接，其中链接的 `href` 目标位于应用基路径 URI 空间中。</span><span class="sxs-lookup"><span data-stu-id="140fd-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="140fd-146">Blazor 路由器负责处理内部导航。</span><span class="sxs-lookup"><span data-stu-id="140fd-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="140fd-147">在许多托管方案中，应用的相对 URL 路径为应用的根目录。</span><span class="sxs-lookup"><span data-stu-id="140fd-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="140fd-148">在这些情况下，应用的相对 URL 基路径为正斜杠 (`<base href="/" />`)，它是 Blazor 应用的默认配置。</span><span class="sxs-lookup"><span data-stu-id="140fd-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="140fd-149">在其他托管方案中（例如 GitHub 页和 IIS 子应用），应用基路径必须设置为应用的服务器相对 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="140fd-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="140fd-150">要设置应用的基路径，请更新 Pages/_Host.cshtml 文件（Blazor 服务器）或 wwwroot/index.html 文件 (Blazor WebAssembly) 的 `<head>` 标记元素中的 `<base>` 标记   。</span><span class="sxs-lookup"><span data-stu-id="140fd-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="140fd-151">将 `href` 属性值设置为 `/{RELATIVE URL PATH}/`（需要尾部反斜杠），其中 `{RELATIVE URL PATH}` 是应用完整相对 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="140fd-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="140fd-152">对于具有非根相对 URL 路径的 Blazor WebAssembly 应用（例如 `<base href="/CoolApp/">`），应用在本地运行时无法查找其资源  。</span><span class="sxs-lookup"><span data-stu-id="140fd-152">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="140fd-153">要在本地开发和测试过程中解决此问题，可提供 path base 参数，用于匹配运行时 `<base>` 标记的 `href` 值  。</span><span class="sxs-lookup"><span data-stu-id="140fd-153">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="140fd-154">不要包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="140fd-154">Don't include a trailing slash.</span></span> <span data-ttu-id="140fd-155">在本地运行应用时，若要传递路径基础参数，请使用 `--pathbase` 选项从应用的目录执行 `dotnet run` 命令：</span><span class="sxs-lookup"><span data-stu-id="140fd-155">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="140fd-156">对于具有相对 URL 路径 `/CoolApp/` (`<base href="/CoolApp/">`) 的 Blazor WebAssembly 应用，命令如下：</span><span class="sxs-lookup"><span data-stu-id="140fd-156">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="140fd-157">Blazor WebAssembly 应用在`http://localhost:port/CoolApp` 本地响应。</span><span class="sxs-lookup"><span data-stu-id="140fd-157">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="140fd-158">部署</span><span class="sxs-lookup"><span data-stu-id="140fd-158">Deployment</span></span>

<span data-ttu-id="140fd-159">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="140fd-159">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
