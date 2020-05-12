---
title: ASP.NET Core 中的浏览器链接
author: ncarandini
description: 说明浏览器链接如何是一种使开发环境与一个或多个 Web 浏览器链接的 Visual Studio 功能。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 619d19ba90298b2455d4a558fea138c86a751f07
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773652"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="6285c-103">ASP.NET Core 中的浏览器链接</span><span class="sxs-lookup"><span data-stu-id="6285c-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="6285c-104">作者：[Nicolò Carandini](https://github.com/ncarandini)、[Mike Wasson](https://github.com/MikeWasson) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="6285c-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="6285c-105">浏览器链接是一种 Visual Studio 功能。</span><span class="sxs-lookup"><span data-stu-id="6285c-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="6285c-106">它在开发环境与一个或多个 Web 浏览器之间创建信道。</span><span class="sxs-lookup"><span data-stu-id="6285c-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="6285c-107">可以使用浏览器链接同时在多个浏览器中刷新 Web 应用，这对于跨浏览器测试非常有用。</span><span class="sxs-lookup"><span data-stu-id="6285c-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="6285c-108">浏览器链接设置</span><span class="sxs-lookup"><span data-stu-id="6285c-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6285c-109">将 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="6285c-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="6285c-110">对于 ASP.NET Core Razor Pages 或 MVC 项目，还需按照 <xref:mvc/views/view-compilation> 中所述的步骤，启用 Razor (.cshtml) 文件的运行时编译  。</span><span class="sxs-lookup"><span data-stu-id="6285c-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="6285c-111">仅当已启用运行时编译时，才会应用 Razor 语法更改。</span><span class="sxs-lookup"><span data-stu-id="6285c-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="6285c-112">将 ASP.NET Core 2.0 项目转换为 ASP.NET Core 2.1 并过渡到 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)时，安装用于浏览器链接功能的 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 包。</span><span class="sxs-lookup"><span data-stu-id="6285c-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="6285c-113">默认情况下，ASP.NET Core 2.1 项目模板使用 `Microsoft.AspNetCore.App` 元包。</span><span class="sxs-lookup"><span data-stu-id="6285c-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6285c-114">ASP.NET Core 2.0“Web 应用”  、“空”  和“Web API”  项目模板使用 [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)，其中包含 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="6285c-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="6285c-115">因此，使用 `Microsoft.AspNetCore.All` 元包无需进一步操作即可使浏览器链接可供使用。</span><span class="sxs-lookup"><span data-stu-id="6285c-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="6285c-116">ASP.NET Core 1.x“Web 应用程序”  项目模板具有 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="6285c-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="6285c-117">其他项目类型需要添加对 `Microsoft.VisualStudio.Web.BrowserLink` 的包引用。</span><span class="sxs-lookup"><span data-stu-id="6285c-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="6285c-118">Configuration</span><span class="sxs-lookup"><span data-stu-id="6285c-118">Configuration</span></span>

<span data-ttu-id="6285c-119">在 `Startup.Configure` 方法中调用 `UseBrowserLink`：</span><span class="sxs-lookup"><span data-stu-id="6285c-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="6285c-120">`UseBrowserLink` 调用通常置于仅在开发环境中启用浏览器链接的 `if` 块内。</span><span class="sxs-lookup"><span data-stu-id="6285c-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="6285c-121">例如：</span><span class="sxs-lookup"><span data-stu-id="6285c-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="6285c-122">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="6285c-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="6285c-123">如何使用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="6285c-123">How to use Browser Link</span></span>

<span data-ttu-id="6285c-124">打开 ASP.NET Core 项目后，Visual Studio 会在“调试目标”  工具栏控件旁显示浏览器链接工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="6285c-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![浏览器链接下拉菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="6285c-126">通过浏览器链接工具栏控件，可以：</span><span class="sxs-lookup"><span data-stu-id="6285c-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="6285c-127">同时在多个浏览器中刷新 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="6285c-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="6285c-128">打开“浏览器链接仪表板”  。</span><span class="sxs-lookup"><span data-stu-id="6285c-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="6285c-129">启用或禁用“浏览器链接”  。</span><span class="sxs-lookup"><span data-stu-id="6285c-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="6285c-130">注意：默认情况下，浏览器链接在 Visual Studio 中处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="6285c-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="6285c-131">启用或禁用 [CSS 自动同步](#enable-or-disable-css-auto-sync)。</span><span class="sxs-lookup"><span data-stu-id="6285c-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="6285c-132">同时在多个浏览器中刷新 Web 应用</span><span class="sxs-lookup"><span data-stu-id="6285c-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="6285c-133">若要选择启动项目时要启动的单个 Web 浏览器，请使用“调试目标”  工具栏控件中的下拉菜单：</span><span class="sxs-lookup"><span data-stu-id="6285c-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 下拉菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="6285c-135">若要同时打开多个浏览器，请从相同下拉菜单中选择“浏览方式...”  。</span><span class="sxs-lookup"><span data-stu-id="6285c-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="6285c-136">按住 <kbd>Ctrl</kbd> 键选择所需浏览器，然后单击“浏览”  ：</span><span class="sxs-lookup"><span data-stu-id="6285c-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![同时打开许多浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="6285c-138">以下屏幕截图显示 Visual Studio，其中包含打开的“索引”视图和两个打开的浏览器：</span><span class="sxs-lookup"><span data-stu-id="6285c-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![与两个浏览器同步示例](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="6285c-140">将鼠标悬停在浏览器链接工具栏控件上方可查看连接到项目的浏览器：</span><span class="sxs-lookup"><span data-stu-id="6285c-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![悬停提示](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="6285c-142">更改“索引”视图，在单击浏览器链接刷新按钮时会更新所有连接的浏览器：</span><span class="sxs-lookup"><span data-stu-id="6285c-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="6285c-144">Browser 链接还适用于从 Visual Studio 外部启动并导航到应用 URL 的浏览器。</span><span class="sxs-lookup"><span data-stu-id="6285c-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="6285c-145">浏览器链接仪表板</span><span class="sxs-lookup"><span data-stu-id="6285c-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="6285c-146">从浏览器链接下拉菜单中打开“浏览器链接仪表板”  窗口，以管理与打开的浏览器的连接：</span><span class="sxs-lookup"><span data-stu-id="6285c-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="6285c-148">如果未连接浏览器，则可以通过选择“在浏览器中查看”  链接来启动非调试会话：</span><span class="sxs-lookup"><span data-stu-id="6285c-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="6285c-150">否则会显示连接的浏览器，以及每个浏览器所显示的页面的路径：</span><span class="sxs-lookup"><span data-stu-id="6285c-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="6285c-152">还可以单击单独的浏览器名称以便仅刷新该浏览器。</span><span class="sxs-lookup"><span data-stu-id="6285c-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="6285c-153">启用或禁用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="6285c-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="6285c-154">在禁用浏览器链接之后重新启用它时，必须刷新浏览器才能重新连接它们。</span><span class="sxs-lookup"><span data-stu-id="6285c-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="6285c-155">启用或禁用 CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="6285c-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="6285c-156">启用 CSS 自动同步后，在对 CSS 文件进行任何更改时，会自动刷新连接的浏览器。</span><span class="sxs-lookup"><span data-stu-id="6285c-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="6285c-157">工作原理</span><span class="sxs-lookup"><span data-stu-id="6285c-157">How it works</span></span>

<span data-ttu-id="6285c-158">浏览器链接使用 [SignalR](xref:signalr/introduction) 在 Visual Studio 与浏览器之间创建信道。</span><span class="sxs-lookup"><span data-stu-id="6285c-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="6285c-159">启用浏览器链接后，Visual Studio 会充当 SignalR 服务器，多个客户端（浏览器）可以连接到它。</span><span class="sxs-lookup"><span data-stu-id="6285c-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="6285c-160">浏览器链接还会在 ASP.NET Core 请求管道中注册一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="6285c-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="6285c-161">此组件从服务器将特殊的 `<script>` 引用注入到每个页面请求中。</span><span class="sxs-lookup"><span data-stu-id="6285c-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="6285c-162">可以通过在浏览器中选择“查看源文件”  并滚动到 `<body>` 标记内容末尾来查看脚本引用：</span><span class="sxs-lookup"><span data-stu-id="6285c-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="6285c-163">源文件不会进行修改。</span><span class="sxs-lookup"><span data-stu-id="6285c-163">Your source files aren't modified.</span></span> <span data-ttu-id="6285c-164">该中间件组件会动态注入脚本引用。</span><span class="sxs-lookup"><span data-stu-id="6285c-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="6285c-165">由于浏览器端代码都是 JavaScript，因此它适用于 SignalR 支持的所有浏览器，而无需浏览器插件。</span><span class="sxs-lookup"><span data-stu-id="6285c-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
