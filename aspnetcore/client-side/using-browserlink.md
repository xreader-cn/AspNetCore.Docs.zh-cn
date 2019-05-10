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
# <a name="browser-link-in-aspnet-core"></a>在 ASP.NET Core 中的浏览器链接

通过[Nicolò Carandini](https://github.com/ncarandini)， [Mike Wasson](https://github.com/MikeWasson)，和[Tom Dykstra](https://github.com/tdykstra)

浏览器链接是一项功能在 Visual Studio 中创建开发环境和一个或多个 web 浏览器之间的通信通道。 可以使用浏览器链接以刷新 web 应用程序在多个浏览器中的，这对于跨浏览器测试很有用。

## <a name="browser-link-setup"></a>浏览器链接安装程序

::: moniker range=">= aspnetcore-2.1"

转换为 ASP.NET Core 2.1，并转换为 ASP.NET Core 2.0 项目时[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，安装[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包浏览器链接功能。 ASP.NET Core 2.1 项目模板使用`Microsoft.AspNetCore.App`默认情况下的元包。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **Web 应用程序**，**空**，并**Web API**项目模板使用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)其中包含的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)。 因此，使用`Microsoft.AspNetCore.All`元包不需要任何进一步的操作，以使浏览器链接可供使用。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **Web 应用程序**项目模板具有的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包。 **空**或**Web API**模板项目需要您添加到包引用`Microsoft.VisualStudio.Web.BrowserLink`。

由于这是一项 Visual Studio 功能，最简单的方法添加到包**空**或**Web API**模板项目是打开**程序包管理器控制台**(**视图** > **其他 Windows** > **程序包管理器控制台**) 并运行以下命令：

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

或者，可以使用**NuGet 包管理器**。 右键单击项目名称在**解决方案资源管理器**，然后选择**管理 NuGet 包**:

![打开 NuGet 包管理器](using-browserlink/_static/open-nuget-package-manager.png)

查找和安装包：

![添加程序包与 NuGet 包管理器](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>配置

在 `Startup.Configure` 方法中：

```csharp
app.UseBrowserLink();
```

该代码通常位于`if`块仅在开发环境中，启用浏览器链接，如下所示：

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments)。

## <a name="how-to-use-browser-link"></a>如何使用浏览器链接

如果你拥有打开 ASP.NET Core 项目，Visual Studio 将旁边显示浏览器链接工具栏控件**调试目标**工具栏控件：

![浏览器链接下拉列表菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

从浏览器链接工具栏控件，您可以：

* 刷新 web 应用程序在多个浏览器中的，一次。
* 打开**浏览器链接仪表板**。
* 启用或禁用**浏览器链接**。 注意:默认情况下，Visual Studio 2017 (15.3) 中，浏览器链接处于禁用状态。
* 启用或禁用[CSS 自动同步](#enable-or-disable-css-auto-sync)。

> [!NOTE]
> 某些 Visual Studio 插件，最值得注意的是*Web 扩展包 2015年*并*Web 扩展包 2017年*，对于浏览器链接，提供扩展的功能，但某些附加功能不与 ASP 配合使用。NET Core 项目。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>同时刷新多个浏览器中的 web 应用

若要选择的单个 web 浏览器启动启动项目时，使用中的下拉列表菜单**调试目标**工具栏控件：

![F5 下拉列表菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

若要同时打开多个浏览器，选择**浏览使用...** 从相同的下拉列表。 按住 CTRL 键以选择所需的浏览器，然后单击**浏览**:

![同时打开多个浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

下面是屏幕截图显示带有索引视图的 Visual Studio 打开和两个打开的浏览器：

![同步两个浏览器示例](using-browserlink/_static/sync-with-two-browsers-example.png)

若要查看的浏览器连接到该项目的浏览器链接工具栏控件上悬停：

![将鼠标悬停在提示](using-browserlink/_static/hoover-tip.png)

更改索引视图，并单击浏览器链接刷新按钮时，会更新所有连接的浏览器：

![浏览器更改同步](using-browserlink/_static/browsers-sync-to-changes.png)

浏览器链接也适用于浏览器从 Visual Studio 外部启动并导航到应用程序 URL。

### <a name="the-browser-link-dashboard"></a>在浏览器链接仪表板

从浏览器链接下拉列表菜单来管理与打开的浏览器的连接打开浏览器链接仪表板：

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

如果浏览器不连接，您可以通过选择启动非调试会话*在浏览器中的查看*链接：

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

否则，连接的浏览器显示与每个浏览器显示的页的路径：

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

如果你愿意，您可以单击列出的浏览器名称，若要刷新该单个浏览器。

### <a name="enable-or-disable-browser-link"></a>启用或禁用浏览器链接

重新启用时浏览器链接后禁用它，您必须刷新浏览器，将它们重新连接。

### <a name="enable-or-disable-css-auto-sync"></a>启用或禁用 CSS 自动同步

启用 CSS 自动同步后，对 CSS 文件进行任何更改时自动刷新连接的浏览器。

## <a name="how-it-works"></a>其工作原理

浏览器链接使用 SignalR 创建 Visual Studio 和浏览器之间的通信通道。 启用浏览器链接后，Visual Studio 将充当多个客户端 （浏览器） 可以连接到的 SignalR 服务器。 浏览器链接还会在 ASP.NET Core 请求管道中注册中间件组件。 此组件将注入特殊`<script>`到每个页面请求来自服务器的引用。 您可以通过选择查看脚本引用**查看源**浏览器并滚动到末尾`<body>`标记的内容：

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

源文件不会被修改。 中间件组件动态插入脚本引用。

由于浏览器端代码是所有 JavaScript，它适用于 SignalR 支持，无需浏览器插件的所有浏览器。
