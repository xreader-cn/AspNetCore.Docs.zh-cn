---
title: 在 ASP.NET Core 中的浏览器链接
author: ncarandini
description: 说明如何浏览器链接是链接与一个或多个 web 浏览器的开发环境的 Visual Studio 功能。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 09/22/2017
uid: client-side/using-browserlink
ms.openlocfilehash: 452ba5149563c186750466f471c7b950f0017614
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894704"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="0b0c3-103">在 ASP.NET Core 中的浏览器链接</span><span class="sxs-lookup"><span data-stu-id="0b0c3-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="0b0c3-104">通过[Nicolò Carandini](https://github.com/ncarandini)， [Mike Wasson](https://github.com/MikeWasson)，和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="0b0c3-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="0b0c3-105">浏览器链接是一项功能在 Visual Studio 中创建开发环境和一个或多个 web 浏览器之间的通信通道。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-105">Browser Link is a feature in Visual Studio that creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="0b0c3-106">可以使用浏览器链接以刷新 web 应用程序在多个浏览器中的，这对于跨浏览器测试很有用。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-106">You can use Browser Link to refresh your web application in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="0b0c3-107">浏览器链接安装程序</span><span class="sxs-lookup"><span data-stu-id="0b0c3-107">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0b0c3-108">转换为 ASP.NET Core 2.1，并转换为 ASP.NET Core 2.0 项目时[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，安装[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包浏览器链接功能。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-108">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for BrowserLink functionality.</span></span> <span data-ttu-id="0b0c3-109">ASP.NET Core 2.1 项目模板使用`Microsoft.AspNetCore.App`默认情况下的元包。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-109">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0b0c3-110">ASP.NET Core 2.0 **Web 应用程序**，**空**，并**Web API**项目模板使用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)其中包含的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-110">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="0b0c3-111">因此，使用`Microsoft.AspNetCore.All`元包不需要任何进一步的操作，以使浏览器链接可供使用。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-111">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0b0c3-112">ASP.NET Core 1.x **Web 应用程序**项目模板具有的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-112">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="0b0c3-113">**空**或**Web API**模板项目需要您添加到包引用`Microsoft.VisualStudio.Web.BrowserLink`。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-113">The **Empty** or **Web API** template projects require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

<span data-ttu-id="0b0c3-114">由于这是一项 Visual Studio 功能，最简单的方法添加到包**空**或**Web API**模板项目是打开**程序包管理器控制台**(**视图** > **其他 Windows** > **程序包管理器控制台**) 并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-114">Since this is a Visual Studio feature, the easiest way to add the package to an **Empty** or **Web API** template project is to open the **Package Manager Console** (**View** > **Other Windows** > **Package Manager Console**) and run the following command:</span></span>

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

<span data-ttu-id="0b0c3-115">或者，可以使用**NuGet 包管理器**。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-115">Alternatively, you can use **NuGet Package Manager**.</span></span> <span data-ttu-id="0b0c3-116">右键单击项目名称在**解决方案资源管理器**，然后选择**管理 NuGet 包**:</span><span class="sxs-lookup"><span data-stu-id="0b0c3-116">Right-click the project name in **Solution Explorer** and choose **Manage NuGet Packages**:</span></span>

![打开 NuGet 包管理器](using-browserlink/_static/open-nuget-package-manager.png)

<span data-ttu-id="0b0c3-118">查找和安装包：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-118">Find and install the package:</span></span>

![添加程序包与 NuGet 包管理器](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="0b0c3-120">配置</span><span class="sxs-lookup"><span data-stu-id="0b0c3-120">Configuration</span></span>

<span data-ttu-id="0b0c3-121">在 `Startup.Configure` 方法中：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-121">In the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="0b0c3-122">该代码通常位于`if`块仅在开发环境中，启用浏览器链接，如下所示：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-122">Usually the code is inside an `if` block that only enables Browser Link in the Development environment, as shown here:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="0b0c3-123">有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-123">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="0b0c3-124">如何使用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="0b0c3-124">How to use Browser Link</span></span>

<span data-ttu-id="0b0c3-125">如果你拥有打开 ASP.NET Core 项目，Visual Studio 将旁边显示浏览器链接工具栏控件**调试目标**工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-125">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![浏览器链接下拉列表菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="0b0c3-127">从浏览器链接工具栏控件，您可以：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-127">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="0b0c3-128">刷新 web 应用程序在多个浏览器中的，一次。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-128">Refresh the web application in several browsers at once.</span></span>
* <span data-ttu-id="0b0c3-129">打开**浏览器链接仪表板**。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-129">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="0b0c3-130">启用或禁用**浏览器链接**。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-130">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="0b0c3-131">注意:默认情况下，Visual Studio 2017 (15.3) 中，浏览器链接处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-131">Note: Browser Link is disabled by default in Visual Studio 2017 (15.3).</span></span>
* <span data-ttu-id="0b0c3-132">启用或禁用[CSS 自动同步](#enable-or-disable-css-auto-sync)。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-132">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

> [!NOTE]
> <span data-ttu-id="0b0c3-133">某些 Visual Studio 插件，最值得注意的是*Web 扩展包 2015年*并*Web 扩展包 2017年*，对于浏览器链接，提供扩展的功能，但某些附加功能不与 ASP 配合使用。NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-133">Some Visual Studio plug-ins, most notably *Web Extension Pack 2015* and *Web Extension Pack 2017*, offer extended functionality for Browser Link, but some of the additional features don't work with ASP.NET Core projects.</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="0b0c3-134">同时刷新多个浏览器中的 web 应用</span><span class="sxs-lookup"><span data-stu-id="0b0c3-134">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="0b0c3-135">若要选择的单个 web 浏览器启动启动项目时，使用中的下拉列表菜单**调试目标**工具栏控件：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-135">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5 下拉列表菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="0b0c3-137">若要同时打开多个浏览器，选择**浏览使用...** 从相同的下拉列表。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-137">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="0b0c3-138">按住 CTRL 键以选择所需的浏览器，然后单击**浏览**:</span><span class="sxs-lookup"><span data-stu-id="0b0c3-138">Hold down the CTRL key to select the browsers you want, and then click **Browse**:</span></span>

![同时打开多个浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="0b0c3-140">下面是屏幕截图显示带有索引视图的 Visual Studio 打开和两个打开的浏览器：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-140">Here's a screenshot showing Visual Studio with the Index view open and two open browsers:</span></span>

![同步两个浏览器示例](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="0b0c3-142">若要查看的浏览器连接到该项目的浏览器链接工具栏控件上悬停：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-142">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![将鼠标悬停在提示](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="0b0c3-144">更改索引视图，并单击浏览器链接刷新按钮时，会更新所有连接的浏览器：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-144">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![浏览器更改同步](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="0b0c3-146">浏览器链接也适用于浏览器从 Visual Studio 外部启动并导航到应用程序 URL。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-146">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the application URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="0b0c3-147">在浏览器链接仪表板</span><span class="sxs-lookup"><span data-stu-id="0b0c3-147">The Browser Link Dashboard</span></span>

<span data-ttu-id="0b0c3-148">从浏览器链接下拉列表菜单来管理与打开的浏览器的连接打开浏览器链接仪表板：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-148">Open the Browser Link Dashboard from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="0b0c3-150">如果浏览器不连接，您可以通过选择启动非调试会话*在浏览器中的查看*链接：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-150">If no browser is connected, you can start a non-debugging session by selecting the *View in Browser* link:</span></span>

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="0b0c3-152">否则，连接的浏览器显示与每个浏览器显示的页的路径：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-152">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="0b0c3-154">如果你愿意，您可以单击列出的浏览器名称，若要刷新该单个浏览器。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-154">If you like, you can click on a listed browser name to refresh that single browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="0b0c3-155">启用或禁用浏览器链接</span><span class="sxs-lookup"><span data-stu-id="0b0c3-155">Enable or disable Browser Link</span></span>

<span data-ttu-id="0b0c3-156">重新启用时浏览器链接后禁用它，您必须刷新浏览器，将它们重新连接。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-156">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="0b0c3-157">启用或禁用 CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="0b0c3-157">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="0b0c3-158">启用 CSS 自动同步后，对 CSS 文件进行任何更改时自动刷新连接的浏览器。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-158">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="0b0c3-159">其工作原理</span><span class="sxs-lookup"><span data-stu-id="0b0c3-159">How it works</span></span>

<span data-ttu-id="0b0c3-160">浏览器链接使用 SignalR 创建 Visual Studio 和浏览器之间的通信通道。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-160">Browser Link uses SignalR to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="0b0c3-161">启用浏览器链接后，Visual Studio 将充当多个客户端 （浏览器） 可以连接到的 SignalR 服务器。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-161">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="0b0c3-162">浏览器链接还会在 ASP.NET Core 请求管道中注册中间件组件。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-162">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="0b0c3-163">此组件将注入特殊`<script>`到每个页面请求来自服务器的引用。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-163">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="0b0c3-164">您可以通过选择查看脚本引用**查看源**浏览器并滚动到末尾`<body>`标记的内容：</span><span class="sxs-lookup"><span data-stu-id="0b0c3-164">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="0b0c3-165">源文件不会被修改。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-165">Your source files aren't modified.</span></span> <span data-ttu-id="0b0c3-166">中间件组件动态插入脚本引用。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-166">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="0b0c3-167">由于浏览器端代码是所有 JavaScript，它适用于 SignalR 支持，无需浏览器插件的所有浏览器。</span><span class="sxs-lookup"><span data-stu-id="0b0c3-167">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
