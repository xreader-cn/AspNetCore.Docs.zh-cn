---
title: 托管和部署 ASP.NET Core Blazor
author: guardrex
description: 了解如何托管和部署 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5a56bbda5bb7727c7dbeaed7f2a91d0dcb6e7e71
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773591"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="c07cf-103">托管和部署 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c07cf-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="c07cf-104">作者：[Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com) 和 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c07cf-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="c07cf-105">发布应用</span><span class="sxs-lookup"><span data-stu-id="c07cf-105">Publish the app</span></span>

<span data-ttu-id="c07cf-106">发布应用，用于发布配置中的部署。</span><span class="sxs-lookup"><span data-stu-id="c07cf-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c07cf-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c07cf-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c07cf-108">从导航栏中选择“生成”   > “发布{应用程序}”  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="c07cf-109">选择“发布目标”  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-109">Select the *publish target*.</span></span> <span data-ttu-id="c07cf-110">若要在本地发布，请选择“文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="c07cf-111">接受“选择文件夹”  字段中的默认位置，或指定其他位置。</span><span class="sxs-lookup"><span data-stu-id="c07cf-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="c07cf-112">选择“发布”  按钮。</span><span class="sxs-lookup"><span data-stu-id="c07cf-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="c07cf-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c07cf-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c07cf-114">使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令发布具有发布配置的应用：</span><span class="sxs-lookup"><span data-stu-id="c07cf-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="c07cf-115">创建部署资产前，发布应用将触发项目依赖项的[还原](/dotnet/core/tools/dotnet-restore)并[生成](/dotnet/core/tools/dotnet-build)生成该项目。</span><span class="sxs-lookup"><span data-stu-id="c07cf-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="c07cf-116">在生成过程期间，将删除未使用的方法和程序集，以减少应用下载大小并缩短加载时间。</span><span class="sxs-lookup"><span data-stu-id="c07cf-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="c07cf-117">Blazor 客户端应用发布到 /bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist 文件夹中  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="c07cf-118">Blazor 服务器端应用将发布到 /bin/Release/{TARGET FRAMEWORK}/publish  文件夹。</span><span class="sxs-lookup"><span data-stu-id="c07cf-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="c07cf-119">文件夹中的资产将部署到 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="c07cf-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="c07cf-120">部署可能是手动或自动化过程，具体取决于使用的开发工具。</span><span class="sxs-lookup"><span data-stu-id="c07cf-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="c07cf-121">应用基路径</span><span class="sxs-lookup"><span data-stu-id="c07cf-121">App base path</span></span>

<span data-ttu-id="c07cf-122">应用基路径是应用的根 URL 路径。 </span><span class="sxs-lookup"><span data-stu-id="c07cf-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="c07cf-123">请考虑以下主应用和 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="c07cf-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="c07cf-124">主应用称为 `MyApp`：</span><span class="sxs-lookup"><span data-stu-id="c07cf-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="c07cf-125">应用实际驻留在 *d:\\MyApp* 中。</span><span class="sxs-lookup"><span data-stu-id="c07cf-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="c07cf-126">在 `https://www.contoso.com/{MYAPP RESOURCE}` 收到请求。</span><span class="sxs-lookup"><span data-stu-id="c07cf-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="c07cf-127">名为 `CoolApp` 的 Blazor 应用是 `MyApp` 的子应用：</span><span class="sxs-lookup"><span data-stu-id="c07cf-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="c07cf-128">子应用实际驻留在 *d:\\MyApp\\CoolApp* 中。</span><span class="sxs-lookup"><span data-stu-id="c07cf-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="c07cf-129">在 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` 收到请求。</span><span class="sxs-lookup"><span data-stu-id="c07cf-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="c07cf-130">如果不为 `CoolApp` 指定其他配置，此方案中的子应用将不知道其在服务器上的位置。</span><span class="sxs-lookup"><span data-stu-id="c07cf-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="c07cf-131">例如，不知道它驻留在相对 URL 路径 `/CoolApp/` 上，应用就无法构造其资源的正确相对 URL。</span><span class="sxs-lookup"><span data-stu-id="c07cf-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="c07cf-132">若要为 Blazor 应用的基路径 `https://www.contoso.com/CoolApp/` 提供配置，请将 `<base>` 标记的 `href` 属性设置为 wwwroot/index.html  文件中的相对根路径：</span><span class="sxs-lookup"><span data-stu-id="c07cf-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="c07cf-133">提供相对 URL 路径，不再根目录中的元件可以构造相对于应用根路径的 URL。</span><span class="sxs-lookup"><span data-stu-id="c07cf-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="c07cf-134">位于目录结构不同级别的组件可生成指向整个应用其他位置的资源链接。</span><span class="sxs-lookup"><span data-stu-id="c07cf-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="c07cf-135">应用基路径也用于截获超链接单击，其中链接的 `href` 目标位于应用基路径 URI 空间中 &mdash; Blazor 路由器可处理内部导航。</span><span class="sxs-lookup"><span data-stu-id="c07cf-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="c07cf-136">在许多托管方案中，应用的相对 URL 路径为应用的根目录。</span><span class="sxs-lookup"><span data-stu-id="c07cf-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="c07cf-137">在这些情况下，应用的相对 URL 基路径为正斜杠 (`<base href="/" />`)，它是 Blazor 应用的默认配置。</span><span class="sxs-lookup"><span data-stu-id="c07cf-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="c07cf-138">在其他托管方案中（例如 GitHub 页和 IIS 子应用），应用基路径必须设置为应用的服务器相对 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="c07cf-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="c07cf-139">要设置应用的基本路径，请更新 wwwroot/index.html 文件的 `<head>` 标记元素中的 `<base>` 标记  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="c07cf-140">将 `href` 属性值设置为 `/{RELATIVE URL PATH}/`（需要尾部反斜杠），其中 `{RELATIVE URL PATH}` 是应用完整相对 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="c07cf-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="c07cf-141">对于配置了非根相对 URL 路径的应用（例如 `<base href="/CoolApp/">`），当在本地运行应用时，将无法查找其资源  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="c07cf-142">要在本地开发和测试过程中解决此问题，可提供 path base 参数，用于匹配运行时 `<base>` 标记的 `href` 值  。</span><span class="sxs-lookup"><span data-stu-id="c07cf-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="c07cf-143">在本地运行应用时，若要传递路径基础参数，请使用 `--pathbase` 选项从应用的目录执行 `dotnet run` 命令：</span><span class="sxs-lookup"><span data-stu-id="c07cf-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="c07cf-144">对于具有相对 URL 路径 `/CoolApp/` (`<base href="/CoolApp/">`) 的应用，命令如下：</span><span class="sxs-lookup"><span data-stu-id="c07cf-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="c07cf-145">应用在 `http://localhost:port/CoolApp` 本地响应。</span><span class="sxs-lookup"><span data-stu-id="c07cf-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="c07cf-146">部署</span><span class="sxs-lookup"><span data-stu-id="c07cf-146">Deployment</span></span>

<span data-ttu-id="c07cf-147">有关部署指南，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="c07cf-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
