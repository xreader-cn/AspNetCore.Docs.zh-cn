---
title: 在 ASP.NET Core 中的浏览器链接
author: ncarandini
description: 说明 Browser Link 如何是一项 Visual Studio 功能，该功能使用一个或多个 web 浏览器链接开发环境。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828265"
---
# <a name="browser-link-in-aspnet-core"></a>在 ASP.NET Core 中的浏览器链接

作者： [Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)和[Tom Dykstra](https://github.com/tdykstra)

浏览器链接是一项 Visual Studio 功能。 它在开发环境和一个或多个 web 浏览器之间创建信道。 你可以使用浏览器链接同时在多个浏览器中刷新你的 web 应用，这对于跨浏览器测试非常有用。

## <a name="browser-link-setup"></a>浏览器链接安装

::: moniker range=">= aspnetcore-3.0"

将[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包添加到项目。 对于 ASP.NET Core Razor Pages 或 MVC 项目，还应按照 <xref:mvc/views/view-compilation>中所述启用 Razor （*cshtml*）文件的运行时编译。 仅当已启用运行时编译时，才会应用 Razor 语法更改。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

将 ASP.NET Core 2.0 项目转换为 ASP.NET Core 2.1 并过渡到[AspNetCore 元包](xref:fundamentals/metapackage-app)时，请安装用于浏览器链接功能的[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包。 默认情况下，ASP.NET Core 2.1 项目模板使用 `Microsoft.AspNetCore.App` 元包。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

"ASP.NET Core 2.0 **Web 应用程序**"、"**空**" 和 " **web API**项目" 模板使用[AspNetCore 元包](xref:fundamentals/metapackage)，其中包含[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)的包引用。 因此，使用 `Microsoft.AspNetCore.All` 元包无需采取进一步的操作即可使用浏览器链接。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **Web 应用程序**项目模板具有的包引用[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)包。 其他项目类型要求您将包引用添加到 `Microsoft.VisualStudio.Web.BrowserLink`。

::: moniker-end

### <a name="configuration"></a>配置

在 `Startup.Configure` 方法中调用 `UseBrowserLink`：

```csharp
app.UseBrowserLink();
```

`UseBrowserLink` 调用通常置于仅在开发环境中启用浏览器链接的 `if` 块内。 例如：

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

有关更多信息，请参见<xref:fundamentals/environments>。

## <a name="how-to-use-browser-link"></a>如何使用浏览器链接

如果你拥有打开 ASP.NET Core 项目，Visual Studio 将旁边显示浏览器链接工具栏控件**调试目标**工具栏控件：

![浏览器链接下拉菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

通过浏览器链接工具栏控件，可以：

* 同时在多个浏览器中刷新 web 应用。
* 打开**浏览器链接仪表板**。
* 启用或禁用**浏览器链接**。 注意：默认情况下，在 Visual Studio 中禁用浏览器链接。
* 启用或禁用[CSS 自动同步](#enable-or-disable-css-auto-sync)。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>同时在多个浏览器中刷新 web 应用

若要选择启动项目时要启动的单个 web 浏览器，请使用 "**调试目标**" 工具栏控件中的下拉菜单：

![F5 下拉菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

若要同时打开多个浏览器，请从相同的下拉选择 "**浏览方式 ...** "。 按住<kbd>Ctrl</kbd>键以选择所需的浏览器，然后单击 "**浏览**"：

![同时打开多个浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

以下屏幕截图显示了 Visual Studio，其中打开了索引视图和两个打开的浏览器：

![与两个浏览器同步示例](using-browserlink/_static/sync-with-two-browsers-example.png)

将鼠标悬停在浏览器链接工具栏控件上，以查看连接到项目的浏览器：

![悬停提示](using-browserlink/_static/hoover-tip.png)

更改 "索引" 视图，当您单击浏览器链接刷新按钮时，将更新所有连接的浏览器：

![浏览器-同步到更改](using-browserlink/_static/browsers-sync-to-changes.png)

Browser 链接还适用于从 Visual Studio 外部启动的浏览器并导航到应用 URL。

### <a name="the-browser-link-dashboard"></a>浏览器链接仪表板

从 "浏览器链接" 下拉菜单打开 "**浏览器链接仪表板**" 窗口，以管理与打开的浏览器的连接：

![browserslink-仪表板](using-browserlink/_static/open-browserlink-dashboard.png)

如果未连接浏览器，则可以通过选择 "**在浏览器中查看**" 链接来启动非调试会话：

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

否则，会显示连接的浏览器，其中显示了每个浏览器显示的页面的路径：

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

你还可以单击单独的浏览器名称以仅刷新该浏览器。

### <a name="enable-or-disable-browser-link"></a>启用或禁用浏览器链接

禁用浏览器链接后，必须刷新浏览器才能将其重新连接。

### <a name="enable-or-disable-css-auto-sync"></a>启用或禁用 CSS 自动同步

启用 CSS 自动同步后，在对 CSS 文件进行任何更改时，将自动刷新连接的浏览器。

## <a name="how-it-works"></a>工作原理

Browser Link 使用[SignalR](xref:signalr/introduction)在 Visual Studio 与浏览器之间创建信道。 启用浏览器链接后，Visual Studio 将充当一个 SignalR 服务器，多个客户端（浏览器）可以连接到该服务器。 浏览器链接还在 ASP.NET Core 请求管道中注册中间件组件。 此组件从服务器插入对每个页面请求的特殊 `<script>` 引用。 可以通过在浏览器中选择 "**查看源**" 并滚动到 `<body>` 标记内容的末尾来查看脚本引用：

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

不会修改您的源文件。 中间件组件动态注入脚本引用。

由于浏览器端代码都是 JavaScript，因此它适用于 SignalR 支持的所有浏览器，而无需浏览器插件。
