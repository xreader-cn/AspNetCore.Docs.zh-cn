---
title: 从 ASP.NET MVC 迁移到 ASP.NET Core MVC
author: ardalis
description: 了解如何开始将 ASP.NET MVC 项目迁移到 ASP.NET Core MVC。
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777041"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>从 ASP.NET MVC 迁移到 ASP.NET Core MVC

作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Steve Smith](https://ardalis.com/)和[Scott Addie](https://scottaddie.com)

本文介绍如何开始将 ASP.NET MVC 项目迁移到[ASP.NET CORE mvc](../mvc/overview.md)。 在此过程中，它突出显示了从 ASP.NET MVC 中更改的许多内容。 从 ASP.NET MVC 迁移是一个多步骤过程，本文介绍了初始设置、基本控制器和视图、静态内容和客户端依赖关系。 其他文章介绍了如何迁移在许多 ASP.NET MVC 项目中找到的配置和标识代码。

> [!NOTE]
> 示例中的版本号可能不是最新的。 可能需要相应地更新项目。

## <a name="create-the-starter-aspnet-mvc-project"></a>创建 starter ASP.NET MVC 项目

为了演示升级，我们首先创建一个 ASP.NET MVC 应用程序。 创建名为*WebApp1*的命名空间，使命名空间与我们在下一步中创建的 ASP.NET Core 项目匹配。

![Visual Studio“新建项目”对话框](mvc/_static/new-project.png)

!["新建 Web 应用程序" 对话框：在 "ASP.NET 模板" 面板中选择的 MVC 项目模板](mvc/_static/new-project-select-mvc-template.png)

*可选：* 将解决方案的名称从*WebApp1*更改为*Mvc5*。 Visual Studio 将显示新的解决方案名称（*Mvc5*），这样就可以更轻松地从下一个项目通知此项目。

## <a name="create-the-aspnet-core-project"></a>创建 ASP.NET Core 项目

使用与上一个项目相同的名称创建新的*空*ASP.NET Core web 应用（*WebApp1*），以便两个项目中的命名空间匹配。 通过具有相同的命名空间，可以更轻松地在两个项目之间复制代码。 必须在与上一个项目相同的目录中创建此项目。

![“新建项目”对话框](mvc/_static/new_core.png)

![New ASP.NET Web 应用程序对话框：在 ASP.NET Core 模板 "面板中选择的空项目模板](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *可选：* 使用 " *Web 应用程序*" 项目模板创建新的 ASP.NET Core 应用程序。 将项目命名为 " *WebApp1*"，并选择**单个用户帐户**的身份验证选项。 将此应用重命名为*FullAspNetCore*。 创建此项目可在转换时节省时间。 您可以查看模板生成的代码以查看最终结果或将代码复制到转换项目。 当您停滞转换步骤以与模板生成的项目进行比较时，这也很有用。

## <a name="configure-the-site-to-use-mvc"></a>将站点配置为使用 MVC

::: moniker range=">= aspnetcore-2.1"

* 面向 .NET Core 时，默认情况下将引用[AspNetCore 元包](xref:fundamentals/metapackage-app)。 此包包含 MVC 应用通常使用的包。 如果目标 .NET Framework，则包引用必须单独列出在项目文件中。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* 面向 .NET Core 时，默认情况下将引用[AspNetCore。](xref:fundamentals/metapackage) 此包包含由 MVC 应用程序使用的包。 如果目标 .NET Framework，则包引用必须单独列出在项目文件中。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* 面向 .NET Core 或 .NET Framework 时，将在项目文件中单独列出包由 MVC 应用程序使用的常用包。

::: moniker-end

`Microsoft.AspNetCore.Mvc`是 ASP.NET Core MVC 框架。 `Microsoft.AspNetCore.StaticFiles`为静态文件处理程序。 ASP.NET Core 运行时是模块化的，你必须明确选择提供静态文件（请参阅[静态文件](xref:fundamentals/static-files)）。

* 打开*Startup.cs*文件并更改代码，使其与以下内容匹配：

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles`扩展方法添加静态文件处理程序。 如前所述，ASP.NET 运行时是模块化的，你必须明确选择提供静态文件。 `UseMvc`扩展方法将添加路由。 有关详细信息，请参阅[应用程序启动](xref:fundamentals/startup)和[路由](xref:fundamentals/routing)。

## <a name="add-a-controller-and-view"></a>添加控制器和视图

在本部分中，你将添加一个最小控制器和视图，用作 ASP.NET MVC 控制器的占位符，以及将在下一部分中迁移的视图。

* 添加 "*控制器*" 文件夹。

* 将名为*HomeController.cs*的**控制器类**添加到 "*控制器*" 文件夹。

![“添加新项”对话框](mvc/_static/add_mvc_ctl.png)

* 添加*Views*文件夹。

* 添加 "*视图"/"主*文件夹"。

* 将名为*Index*的** Razor视图**添加到*Views/Home*文件夹中。

![“添加新项”对话框](mvc/_static/view.png)

项目结构如下所示：

![显示 WebApp1 的文件和文件夹的解决方案资源管理器](mvc/_static/project-structure-controller-view.png)

将*Views/Home/Index. cshtml*文件的内容替换为以下内容：

```html
<h1>Hello world!</h1>
```

运行应用。

![在 Microsoft Edge 中打开的 Web 应用](mvc/_static/hello-world.png)

有关详细信息，请参阅[控制器](xref:mvc/controllers/actions)和[视图](xref:mvc/views/overview)。

现在，我们有了最小的工作 ASP.NET Core 项目，接下来可以开始从 ASP.NET MVC 项目迁移功能。 我们需要移动以下内容：

* 客户端内容（CSS、字体和脚本）

* 控制器

* 视图

* 模型

* 销售

* 筛选器

* 登录/注销， Identity （在下一教程中完成此操作。）

## <a name="controllers-and-views"></a>控制器和视图

* 将 ASP.NET MVC `HomeController`中的每个方法复制到新`HomeController`的。 请注意，在 ASP.NET MVC 中，内置模板的控制器操作方法返回类型为[ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);在 ASP.NET Core MVC 中，操作方法将`IActionResult`改为返回。 `ActionResult`实现`IActionResult`，因此无需更改操作方法的返回类型。

* 将 ASP.NET MVC 项目中的*About*、 *Contact*和*Index.cshtml* Razor视图文件复制到 ASP.NET Core 项目。

* 运行 ASP.NET Core 应用并测试每个方法。 尚未迁移布局文件或样式，因此呈现的视图仅包含视图文件中的内容。 你没有`About`和`Contact`视图的布局文件生成链接，因此你必须从浏览器调用它们（将**4492**替换为项目中使用的端口号）。

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![联系人页](mvc/_static/contact-page.png)

请注意缺少样式和菜单项。 此问题将在下一部分得以解决。

## <a name="static-content"></a>静态内容

在以前版本的 ASP.NET MVC 中，静态内容是从 web 项目的根托管的，与服务器端文件混合。 在 ASP.NET Core 中，静态内容承载于*wwwroot*文件夹中。 需要将静态内容从旧的 ASP.NET MVC 应用复制到 ASP.NET Core 项目中的*wwwroot*文件夹。 在此示例转换中：

* 将旧 MVC 项目中的*favicon*文件复制到 ASP.NET Core 项目中的*wwwroot*文件夹。

旧的 ASP.NET MVC 项目使用[启动](https://getbootstrap.com/)来设置其样式，并将启动文件存储在 "*内容*" 和 "*脚本*" 文件夹中。 生成旧的 ASP.NET MVC 项目的模板引用布局文件（*Views/Shared/_Layout*）中的启动。 可以将 ASP.NET MVC 项目中的*node.js*和*启动 .css*文件复制到新项目中的*wwwroot*文件夹。 相反，我们将在下一节中使用 Cdn 添加对启动（和其他客户端库）的支持。

## <a name="migrate-the-layout-file"></a>迁移布局文件

* 将 *_ViewStart*的 ASP.NET 文件从旧的 MVC 项目的*views*文件夹复制到 ASP.NET Core 项目的*views*文件夹中。 未在 ASP.NET Core MVC 中更改 *_ViewStart cshtml*文件。

* 创建*视图/共享*文件夹。

* *可选：* 将*FullAspNetCore* MVC 项目的*views*文件夹中 *_ViewImports*为 ASP.NET Core 项目的*views*文件夹。 删除 *_ViewImports cshtml*文件中的任何命名空间声明。 *_ViewImports 的 cshtml*文件提供所有视图文件的命名空间，并提供[标记帮助](xref:mvc/views/tag-helpers/intro)程序。 标记帮助程序在新的布局文件中使用。 *_ViewImports* ASP.NET Core 的新文件。

* 将 *_Layout*的 ASP.NET 文件从旧的 MVC 项目的*视图/共享*文件夹复制到 ASP.NET Core 项目的*视图/共享*文件夹中。

打开 *_Layout 的 cshtml*文件并进行以下更改（完成的代码如下所示）：

* 替换`@Styles.Render("~/Content/css")`为加载`<link>` *启动 .css*的元素（见下文）。

* 删除 `@Scripts.Render("~/bundles/modernizr")`。

* 注释掉`@Html.Partial("_LoginPartial")`行（将该行环绕`@*...*@`）。 有关详细信息，请参阅[迁移Identity身份验证和 ASP.NET Core](xref:migration/identity)

* 替换`@Scripts.Render("~/bundles/jquery")`为`<script>`元素（见下文）。

* 替换`@Scripts.Render("~/bundles/bootstrap")`为`<script>`元素（见下文）。

用于启动 CSS 的替换标记包含：

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 和启动 JavaScript 的替换标记包含：

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

更新后的 *_Layout cshtml*文件如下所示：

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

在浏览器中查看站点。 它现在应正确加载，并具有所需的样式。

* *可选：* 你可能想要尝试使用新的布局文件。 对于此项目，您可以从*FullAspNetCore*项目复制布局文件。 新的布局文件使用[标记帮助](xref:mvc/views/tag-helpers/intro)程序，并具有其他改进功能。

## <a name="configure-bundling-and-minification"></a>配置捆绑和缩小

有关如何配置绑定和缩减的信息，请参阅[捆绑和缩减](../client-side/bundling-and-minification.md)。

## <a name="solve-http-500-errors"></a>解决 HTTP 500 错误

有许多问题可能会导致 HTTP 500 错误消息，其中不包含问题根源的相关信息。 例如，如果*Views/_ViewImports cshtml*文件包含项目中不存在的命名空间，则会收到 HTTP 500 错误。 默认情况下，在 ASP.NET Core 应用`UseDeveloperExceptionPage`中，会将扩展`IApplicationBuilder`添加到，并在配置为*开发*时执行。 下面的代码对此进行了详细说明：

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core 将 web 应用中的未处理异常转换为 HTTP 500 错误响应。 通常，这些响应中不包含错误详细信息，以防止泄露有关服务器的可能敏感信息。 有关详细信息，请参阅 "[处理错误](../fundamentals/error-handling.md) **" 中的使用开发者异常页**。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
