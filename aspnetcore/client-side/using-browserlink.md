---
title: ASP.NET Core 中的浏览器链接
author: ncarandini
description: 说明浏览器链接如何是一种使开发环境与一个或多个 Web 浏览器链接的 Visual Studio 功能。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: ab4ca78fa50768ff66536608a7cf03e73aecf73a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628815"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core 中的浏览器链接

作者：[Nicolò Carandini](https://github.com/ncarandini)、[Mike Wasson](https://github.com/MikeWasson) 和 [Tom Dykstra](https://github.com/tdykstra)

浏览器链接是一种 Visual Studio 功能。 它在开发环境与一个或多个 Web 浏览器之间创建信道。 可以使用浏览器链接同时在多个浏览器中刷新 Web 应用，这对于跨浏览器测试非常有用。

## <a name="browser-link-setup"></a>浏览器链接设置

::: moniker range=">= aspnetcore-3.0"

将 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 包添加到项目。 对于 ASP.NET Core Razor Pages 或 MVC 项目，还需按照 <xref:mvc/views/view-compilation> 中所述的步骤，启用 Razor (.cshtml) 文件的运行时编译  。 仅当已启用运行时编译时，才会应用 Razor 语法更改。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

将 ASP.NET Core 2.0 项目转换为 ASP.NET Core 2.1 并过渡到 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)时，安装用于浏览器链接功能的 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 包。 默认情况下，ASP.NET Core 2.1 项目模板使用 `Microsoft.AspNetCore.App` 元包。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0“Web 应用”  、“空”  和“Web API”  项目模板使用 [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)，其中包含 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 的包引用。 因此，使用 `Microsoft.AspNetCore.All` 元包无需进一步操作即可使浏览器链接可供使用。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x“Web 应用程序”  项目模板具有 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 包的包引用。 其他项目类型需要添加对 `Microsoft.VisualStudio.Web.BrowserLink` 的包引用。

::: moniker-end

### <a name="configuration"></a>Configuration

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

有关详细信息，请参阅 <xref:fundamentals/environments>。

## <a name="how-to-use-browser-link"></a>如何使用浏览器链接

打开 ASP.NET Core 项目后，Visual Studio 会在“调试目标”  工具栏控件旁显示浏览器链接工具栏控件：

![浏览器链接下拉菜单](using-browserlink/_static/browserLink-dropdown-menu.png)

通过浏览器链接工具栏控件，可以：

* 同时在多个浏览器中刷新 Web 应用。
* 打开“浏览器链接仪表板”  。
* 启用或禁用“浏览器链接”  。 注意：默认情况下，浏览器链接在 Visual Studio 中处于禁用状态。
* 启用或禁用 [CSS 自动同步](#enable-or-disable-css-auto-sync)。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>同时在多个浏览器中刷新 Web 应用

若要选择启动项目时要启动的单个 Web 浏览器，请使用“调试目标”  工具栏控件中的下拉菜单：

![F5 下拉菜单](using-browserlink/_static/debug-target-dropdown-menu.png)

若要同时打开多个浏览器，请从相同下拉菜单中选择“浏览方式...”  。 按住 <kbd>Ctrl</kbd> 键选择所需浏览器，然后单击“浏览”  ：

![同时打开许多浏览器](using-browserlink/_static/open-many-browsers-at-once.png)

以下屏幕截图显示 Visual Studio，其中包含打开的“索引”视图和两个打开的浏览器：

![与两个浏览器同步示例](using-browserlink/_static/sync-with-two-browsers-example.png)

将鼠标悬停在浏览器链接工具栏控件上方可查看连接到项目的浏览器：

![悬停提示](using-browserlink/_static/hoover-tip.png)

更改“索引”视图，在单击浏览器链接刷新按钮时会更新所有连接的浏览器：

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Browser 链接还适用于从 Visual Studio 外部启动并导航到应用 URL 的浏览器。

### <a name="the-browser-link-dashboard"></a>浏览器链接仪表板

从浏览器链接下拉菜单中打开“浏览器链接仪表板”  窗口，以管理与打开的浏览器的连接：

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

如果未连接浏览器，则可以通过选择“在浏览器中查看”  链接来启动非调试会话：

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

否则会显示连接的浏览器，以及每个浏览器所显示的页面的路径：

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

还可以单击单独的浏览器名称以便仅刷新该浏览器。

### <a name="enable-or-disable-browser-link"></a>启用或禁用浏览器链接

在禁用浏览器链接之后重新启用它时，必须刷新浏览器才能重新连接它们。

### <a name="enable-or-disable-css-auto-sync"></a>启用或禁用 CSS 自动同步

启用 CSS 自动同步后，在对 CSS 文件进行任何更改时，会自动刷新连接的浏览器。

## <a name="how-it-works"></a>工作原理

浏览器链接使用 [SignalR](xref:signalr/introduction) 在 Visual Studio 与浏览器之间创建信道。 启用浏览器链接后，Visual Studio 会充当 SignalR 服务器，多个客户端（浏览器）可以连接到它。 浏览器链接还会在 ASP.NET Core 请求管道中注册一个中间件组件。 此组件从服务器将特殊的 `<script>` 引用注入到每个页面请求中。 可以通过在浏览器中选择“查看源文件”  并滚动到 `<body>` 标记内容末尾来查看脚本引用：

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

源文件不会进行修改。 该中间件组件会动态注入脚本引用。

由于浏览器端代码都是 JavaScript，因此它适用于 SignalR 支持的所有浏览器，而无需浏览器插件。
