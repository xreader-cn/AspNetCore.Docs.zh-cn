---
title: 托管和部署 ASP.NET Core Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
no-loc:
- appsettings.json
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: e7bc44b396b46e2ac3e0279520c7cc8ea6679f5a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279767"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="0546e-103">托管和部署 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0546e-103">Host and deploy ASP.NET Core Blazor</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="0546e-104">发布应用</span><span class="sxs-lookup"><span data-stu-id="0546e-104">Publish the app</span></span>

<span data-ttu-id="0546e-105">发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="0546e-105">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0546e-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0546e-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0546e-107">从导航栏中选择“生成” > “发布{应用程序}”。</span><span class="sxs-lookup"><span data-stu-id="0546e-107">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="0546e-108">选择“发布目标”。</span><span class="sxs-lookup"><span data-stu-id="0546e-108">Select the *publish target*.</span></span> <span data-ttu-id="0546e-109">若要在本地发布，请选择“文件夹”。</span><span class="sxs-lookup"><span data-stu-id="0546e-109">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="0546e-110">接受“选择文件夹”字段中的默认位置，或指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="0546e-110">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="0546e-111">选择 `Publish` 按钮。</span><span class="sxs-lookup"><span data-stu-id="0546e-111">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0546e-112">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0546e-112">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0546e-113">选择“生成” > “发布到文件夹” 。</span><span class="sxs-lookup"><span data-stu-id="0546e-113">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="0546e-114">确认用于接收发布的资产的文件夹，并选择 `Publish`。</span><span class="sxs-lookup"><span data-stu-id="0546e-114">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0546e-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0546e-115">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0546e-116">使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令发布具有发布配置的应用：</span><span class="sxs-lookup"><span data-stu-id="0546e-116">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="0546e-117">创建部署资产前，发布应用将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)生成该项目。</span><span class="sxs-lookup"><span data-stu-id="0546e-117">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="0546e-118">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="0546e-118">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="0546e-119">发布位置：</span><span class="sxs-lookup"><span data-stu-id="0546e-119">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="0546e-120">独立：将应用发布到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0546e-120">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="0546e-121">若要将应用部署为静态站点，请将 `wwwroot` 文件夹的内容复制到静态站点主机。</span><span class="sxs-lookup"><span data-stu-id="0546e-121">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="0546e-122">托管：客户端 Blazor WebAssembly 应用与服务器应用的其他任何静态 Web 资产一起发布到服务器应用的 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0546e-122">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="0546e-123">将 `publish` 文件夹的内容部署到主机。</span><span class="sxs-lookup"><span data-stu-id="0546e-123">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="0546e-124">Blazor Server：将应用发布到 `/bin/Release/{TARGET FRAMEWORK}/publish` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0546e-124">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="0546e-125">将 `publish` 文件夹的内容部署到主机。</span><span class="sxs-lookup"><span data-stu-id="0546e-125">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="0546e-126">文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="0546e-126">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="0546e-127">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="0546e-127">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="0546e-128">应用基路径</span><span class="sxs-lookup"><span data-stu-id="0546e-128">App base path</span></span>

<span data-ttu-id="0546e-129">应用基路径是应用的根 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="0546e-129">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="0546e-130">请考虑使用下列 ASP.NET Core 应用和 Blazor 子应用：</span><span class="sxs-lookup"><span data-stu-id="0546e-130">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="0546e-131">ASP.NET Core 应用名为 `MyApp`：</span><span class="sxs-lookup"><span data-stu-id="0546e-131">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="0546e-132">该应用实际驻留在 `d:/MyApp` 中。</span><span class="sxs-lookup"><span data-stu-id="0546e-132">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="0546e-133">在 `https://www.contoso.com/{MYAPP RESOURCE}` 收到请求。</span><span class="sxs-lookup"><span data-stu-id="0546e-133">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="0546e-134">名为 `CoolApp` 的 Blazor 应用是 `MyApp` 的子应用：</span><span class="sxs-lookup"><span data-stu-id="0546e-134">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="0546e-135">该子应用实际驻留在 `d:/MyApp/CoolApp` 中。</span><span class="sxs-lookup"><span data-stu-id="0546e-135">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="0546e-136">在 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` 收到请求。</span><span class="sxs-lookup"><span data-stu-id="0546e-136">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="0546e-137">如果不为 `CoolApp` 指定其他配置，此方案中的子应用将不知道其在服务器上的位置。</span><span class="sxs-lookup"><span data-stu-id="0546e-137">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="0546e-138">例如，不知道它驻留在相对 URL 路径 `/CoolApp/` 上，应用就无法构造其资源的正确相对 URL。</span><span class="sxs-lookup"><span data-stu-id="0546e-138">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="0546e-139">若要为 Blazor 应用的基路径 `https://www.contoso.com/CoolApp/` 提供配置，请将 `<base>` 标记的 `href` 属性设置为 `wwwroot/index.html` 文件 (Blazor WebAssembly) 或 `Pages/_Host.cshtml` 文件 (Blazor Server) 中的相对根路径。</span><span class="sxs-lookup"><span data-stu-id="0546e-139">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="0546e-140">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="0546e-140">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="0546e-141">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="0546e-141">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="0546e-142">此外，Blazor Server应用还通过在应用的请求管道 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>，设置服务器端基路径：</span><span class="sxs-lookup"><span data-stu-id="0546e-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="0546e-143">提供相对 URL 路径，不再根目录中的元件可以构造相对于应用根路径的 URL。</span><span class="sxs-lookup"><span data-stu-id="0546e-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="0546e-144">位于目录结构不同级别的组件可生成指向整个应用其他位置的资源链接。</span><span class="sxs-lookup"><span data-stu-id="0546e-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="0546e-145">应用基路径也用于拦截所选超链接，其中链接的 `href` 目标位于应用基路径 URI 空间中。</span><span class="sxs-lookup"><span data-stu-id="0546e-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="0546e-146">Blazor 路由器负责处理内部导航。</span><span class="sxs-lookup"><span data-stu-id="0546e-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="0546e-147">在许多托管方案中，应用的相对 URL 路径为应用的根目录。</span><span class="sxs-lookup"><span data-stu-id="0546e-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="0546e-148">在这些情况下，应用的相对 URL 基路径为正斜杠 (`<base href="/" />`)，它是 Blazor 应用的默认配置。</span><span class="sxs-lookup"><span data-stu-id="0546e-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="0546e-149">在其他托管方案中（例如 GitHub 页和 IIS 子应用），应用基路径必须设置为应用的服务器相对 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="0546e-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="0546e-150">若要设置应用的基路径，请更新 `Pages/_Host.cshtml` 文件（Blazor Server）或 `wwwroot/index.html` 文件 (Blazor WebAssembly) 的 `<head>` 标记元素中的 `<base>` 标记。</span><span class="sxs-lookup"><span data-stu-id="0546e-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="0546e-151">将 `href` 属性值设置为 `/{RELATIVE URL PATH}/` (Blazor WebAssembly) 或 `~/{RELATIVE URL PATH}/` (Blazor Server)。</span><span class="sxs-lookup"><span data-stu-id="0546e-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="0546e-152">尾部反斜杠是必需项。</span><span class="sxs-lookup"><span data-stu-id="0546e-152">**The trailing slash is required.**</span></span> <span data-ttu-id="0546e-153">占位符 `{RELATIVE URL PATH}` 是应用的完整相对 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="0546e-153">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="0546e-154">对于具有非根相对 URL 路径（例如 `<base href="/CoolApp/">`）的 Blazor WebAssembly 应用，应用在本地运行时找不到其资源。</span><span class="sxs-lookup"><span data-stu-id="0546e-154">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="0546e-155">要在本地开发和测试过程中解决此问题，可提供 path base 参数，用于匹配运行时 `<base>` 标记的 `href` 值。</span><span class="sxs-lookup"><span data-stu-id="0546e-155">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="0546e-156">**不要包含尾部反斜杠。**</span><span class="sxs-lookup"><span data-stu-id="0546e-156">**Don't include a trailing slash.**</span></span> <span data-ttu-id="0546e-157">在本地运行应用时，若要传递路径基础参数，请使用 `--pathbase` 选项从应用的目录执行 `dotnet run` 命令：</span><span class="sxs-lookup"><span data-stu-id="0546e-157">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="0546e-158">对于具有相对 URL 路径 `/CoolApp/` (`<base href="/CoolApp/">`) 的 Blazor WebAssembly 应用，命令如下：</span><span class="sxs-lookup"><span data-stu-id="0546e-158">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="0546e-159">Blazor WebAssembly 应用在 `http://localhost:port/CoolApp` 本地响应。</span><span class="sxs-lookup"><span data-stu-id="0546e-159">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="0546e-160">**Blazor Server `MapFallbackToPage` 配置**</span><span class="sxs-lookup"><span data-stu-id="0546e-160">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="0546e-161">在 `Startup.Configure` 中向 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 添加以下路径：</span><span class="sxs-lookup"><span data-stu-id="0546e-161">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="0546e-162">占位符 `{RELATIVE PATH}` 是服务器上的非根路径。</span><span class="sxs-lookup"><span data-stu-id="0546e-162">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="0546e-163">例如，如果应用的非根 URL 是 `https://{HOST}:{PORT}/CoolApp/`，`CoolApp` 就是占位符段：</span><span class="sxs-lookup"><span data-stu-id="0546e-163">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="0546e-164">托管多个 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="0546e-164">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="0546e-165">有关在托管的 Blazor 解决方案中托管多个 Blazor WebAssembly 应用的详细信息，请参阅 <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>。</span><span class="sxs-lookup"><span data-stu-id="0546e-165">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="0546e-166">部署</span><span class="sxs-lookup"><span data-stu-id="0546e-166">Deployment</span></span>

<span data-ttu-id="0546e-167">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="0546e-167">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
