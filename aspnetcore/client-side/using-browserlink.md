---
title: ASP.NET Core 中的浏览器链接
author: ncarandini
description: 说明 Browser Link 如何是一项 Visual Studio 功能，该功能使用一个或多个 web 浏览器链接开发环境。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962786"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="b30ac-103">ASP.NET Core 中的浏览器链接</span><span class="sxs-lookup"><span data-stu-id="b30ac-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="b30ac-104">作者： [Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b30ac-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b30ac-105">Browser 链接是 Visual Studio 中的一项功能，它在开发环境和一个或多个 web 浏览器之间创建信道。</span><span class="sxs-lookup"><span data-stu-id="b30ac-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="b30ac-106">您可以使用浏览器链接一次刷新多个浏览器中的 web 应用程序，这对于跨浏览器测试非常有用。</span><span class="sxs-lookup"><span data-stu-id="b30ac-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="b30ac-107">浏览器链接安装</span><span class="sxs-lookup"><span data-stu-id="b30ac-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="b30ac-108">将 ASP.NET Core 2.0 项目转换为 ASP.NET Core 2.1 并过渡到[AspNetCore 元包](xref:fundamentals/metapackage-app)时，请安装浏览器链接功能的[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包。</span><span class="sxs-lookup"><span data-stu-id="b30ac-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="b30ac-109">默认情况下，ASP.NET Core 2.1 项目模板使用 `Microsoft.AspNetCore.App` 元包。</span><span class="sxs-lookup"><span data-stu-id="b30ac-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b30ac-110">"ASP.NET Core 2.0 **Web 应用程序**"、"**空**" 和 " **web API**项目" 模板使用[AspNetCore 元包](xref:fundamentals/metapackage)，其中包含[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)的包引用。</span><span class="sxs-lookup"><span data-stu-id="b30ac-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="b30ac-111">因此，使用 `Microsoft.AspNetCore.All` 元包无需采取进一步的操作即可使用浏览器链接。</span><span class="sxs-lookup"><span data-stu-id="b30ac-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="b30ac-112">ASP.NET Core 1.x **Web 应用程序**项目模板具有[VisualStudio 浏览器链接](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包的包引用。</span><span class="sxs-lookup"><span data-stu-id="b30ac-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="b30ac-113">**空**或**Web API**模板项目要求将包引用添加到 `Microsoft.VisualStudio.Web.BrowserLink`。</span><span class="sxs-lookup"><span data-stu-id="b30ac-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="b30ac-114">由于这是一项 Visual Studio 功能，将包添加到**空**或**Web API**模板项目的最简单方法是打开**包管理器控制台**（>**其他 Windows** >**包管理器控制台**中**查看**）并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="b30ac-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="b30ac-115">或者，你可以使用**NuGet 包管理器**。</span><span class="sxs-lookup"><span data-stu-id="b30ac-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="b30ac-116">右键单击 "**解决方案资源管理器**中的项目名称，然后选择"**管理 NuGet 包**"：</span><span class="sxs-lookup"><span data-stu-id="b30ac-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![打开 NuGet 包管理器](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="b30ac-118">查找并安装包：</span><span class="sxs-lookup"><span data-stu-id="b30ac-118">Find and install the package:</span></span>

![添加包和 NuGet 包管理器](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="b30ac-120">配置</span><span class="sxs-lookup"><span data-stu-id="b30ac-120">Configuration</span></span>

<span data-ttu-id="b30ac-121">在 `Startup.Configure` 方法中：</span><span class="sxs-lookup"><span data-stu-id="b30ac-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="b30ac-122">通常，代码位于仅在开发环境中启用浏览器链接的 `if` 块中，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b30ac-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="b30ac-123">有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="b30ac-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="b30ac-124">如何使用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="b30ac-124">How to use Browser Link</span></span>

<span data-ttu-id="b30ac-125">打开 ASP.NET Core 项目后，Visual Studio 会在 "**调试目标**" 工具栏控件旁显示 "浏览器链接" 工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="b30ac-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![浏览器链接下拉菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="b30ac-127">通过浏览器链接工具栏控件，可以：</span><span class="sxs-lookup"><span data-stu-id="b30ac-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="b30ac-128">同时在多个浏览器中刷新 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="b30ac-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="b30ac-129">打开**浏览器链接仪表板**。</span><span class="sxs-lookup"><span data-stu-id="b30ac-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="b30ac-130">启用或禁用**浏览器链接**。</span><span class="sxs-lookup"><span data-stu-id="b30ac-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="b30ac-131">注意：默认情况下，在 Visual Studio 2017 （15.3）中禁用浏览器链接。</span><span class="sxs-lookup"><span data-stu-id="b30ac-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="b30ac-132">启用或禁用[CSS 自动同步](#enable-or-disable-css-auto-sync)。</span><span class="sxs-lookup"><span data-stu-id="b30ac-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="b30ac-133">某些 Visual Studio 插件（最值得注意的是*Web 扩展包 2015*和*web 扩展包 2017*）为浏览器链接提供扩展功能，但某些其他功能不适用于 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="b30ac-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="b30ac-134">同时在多个浏览器中刷新 web 应用</span><span class="sxs-lookup"><span data-stu-id="b30ac-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="b30ac-135">若要选择启动项目时要启动的单个 web 浏览器，请使用 "**调试目标**" 工具栏控件中的下拉菜单：</span><span class="sxs-lookup"><span data-stu-id="b30ac-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 下拉菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="b30ac-137">若要同时打开多个浏览器，请从相同的下拉选择 "**浏览方式 ...** "。</span><span class="sxs-lookup"><span data-stu-id="b30ac-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="b30ac-138">按住 CTRL 键以选择所需的浏览器，然后单击 "**浏览**"：</span><span class="sxs-lookup"><span data-stu-id="b30ac-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![同时打开多个浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="b30ac-140">下面是一个屏幕截图，其中显示了具有索引视图打开的 Visual Studio 和两个打开的浏览器：</span><span class="sxs-lookup"><span data-stu-id="b30ac-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![与两个浏览器同步示例](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="b30ac-142">将鼠标悬停在浏览器链接工具栏控件上，以查看连接到项目的浏览器：</span><span class="sxs-lookup"><span data-stu-id="b30ac-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![悬停提示](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="b30ac-144">更改 "索引" 视图，当您单击浏览器链接刷新按钮时，将更新所有连接的浏览器：</span><span class="sxs-lookup"><span data-stu-id="b30ac-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![浏览器-同步到更改](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="b30ac-146">Browser 链接还适用于从 Visual Studio 外部启动的浏览器并导航到应用程序 URL。</span><span class="sxs-lookup"><span data-stu-id="b30ac-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="b30ac-147">浏览器链接仪表板</span><span class="sxs-lookup"><span data-stu-id="b30ac-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="b30ac-148">从浏览器链接下拉菜单中打开浏览器链接仪表板，以管理与打开的浏览器的连接：</span><span class="sxs-lookup"><span data-stu-id="b30ac-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![browserslink-仪表板](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="b30ac-150">如果未连接浏览器，则可以通过选择 "*在浏览器中查看*" 链接来启动非调试会话：</span><span class="sxs-lookup"><span data-stu-id="b30ac-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![浏览器链接-无连接](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="b30ac-152">否则，会显示连接的浏览器，其中显示了每个浏览器显示的页面的路径：</span><span class="sxs-lookup"><span data-stu-id="b30ac-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![浏览器链接-双连接](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="b30ac-154">如果需要，可以单击列出的浏览器名称来刷新该单一浏览器。</span><span class="sxs-lookup"><span data-stu-id="b30ac-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="b30ac-155">启用或禁用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="b30ac-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="b30ac-156">禁用浏览器链接后，必须刷新浏览器才能将其重新连接。</span><span class="sxs-lookup"><span data-stu-id="b30ac-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="b30ac-157">启用或禁用 CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="b30ac-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="b30ac-158">启用 CSS 自动同步后，在对 CSS 文件进行任何更改时，将自动刷新连接的浏览器。</span><span class="sxs-lookup"><span data-stu-id="b30ac-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="b30ac-159">工作原理</span><span class="sxs-lookup"><span data-stu-id="b30ac-159">How it works</span></span>

<span data-ttu-id="b30ac-160">Browser Link 使用 SignalR 在 Visual Studio 与浏览器之间创建信道。</span><span class="sxs-lookup"><span data-stu-id="b30ac-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="b30ac-161">启用浏览器链接后，Visual Studio 将充当一个 SignalR 服务器，多个客户端（浏览器）可以连接到该服务器。</span><span class="sxs-lookup"><span data-stu-id="b30ac-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="b30ac-162">浏览器链接还在 ASP.NET Core 请求管道中注册中间件组件。</span><span class="sxs-lookup"><span data-stu-id="b30ac-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="b30ac-163">此组件从服务器插入对每个页面请求的特殊 `<script>` 引用。</span><span class="sxs-lookup"><span data-stu-id="b30ac-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="b30ac-164">可以通过在浏览器中选择 "**查看源**" 并滚动到 `<body>` 标记内容的末尾来查看脚本引用：</span><span class="sxs-lookup"><span data-stu-id="b30ac-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="b30ac-165">不会修改您的源文件。</span><span class="sxs-lookup"><span data-stu-id="b30ac-165">Your source files aren't modified.</span></span> <span data-ttu-id="b30ac-166">中间件组件动态注入脚本引用。</span><span class="sxs-lookup"><span data-stu-id="b30ac-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="b30ac-167">由于浏览器端代码都是 JavaScript，因此它适用于 SignalR 支持的所有浏览器，而无需浏览器插件。</span><span class="sxs-lookup"><span data-stu-id="b30ac-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
