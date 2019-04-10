---
title: 将从 ASP.NET MVC 迁移到 ASP.NET Core MVC
author: ardalis
description: 了解如何开始迁移到 ASP.NET Core MVC ASP.NET MVC 项目。
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: a85b9f15be8ad9ca66b20ef1f4422fe67806a797
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468535"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>将从 ASP.NET MVC 迁移到 ASP.NET Core MVC

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Daniel Roth](https://github.com/danroth27)， [Steve Smith](https://ardalis.com/)，和[Scott Addie](https://scottaddie.com)

这篇文章演示如何开始迁移到 ASP.NET MVC 项目[ASP.NET Core MVC](../mvc/overview.md)。 在过程中，它重点介绍了许多的 ASP.NET MVC 中已更改的事项。 从 ASP.NET MVC 迁移是一个多步骤过程，这篇文章介绍初始设置、 基本控制器和视图、 静态内容和客户端的依赖项。 其他文章介绍了迁移配置和多个 ASP.NET MVC 项目中的标识代码。

> [!NOTE]
> 这些示例中的版本号可能不是最新。 您可能需要相应地更新你的项目。

## <a name="create-the-starter-aspnet-mvc-project"></a>创建入门级的 ASP.NET MVC 项目

若要演示升级，我们将首先创建一个 ASP.NET MVC 应用。 创建同名*WebApp1*使命名空间匹配我们在下一步中创建 ASP.NET Core 项目。

![Visual Studio 新项目对话框](mvc/_static/new-project.png)

![新建 Web 应用程序对话框：ASP.NET 模板窗格中选择 MVC 项目模板](mvc/_static/new-project-select-mvc-template.png)

*可选：* 更改从解决方案的名称*WebApp1*到*Mvc5*。 Visual Studio 将显示新的解决方案名称 (*Mvc5*)，从而更轻松地判断此项目从下一个项目。

## <a name="create-the-aspnet-core-project"></a>创建 ASP.NET Core 项目

创建一个新*空*ASP.NET Core 与以前的项目同名的 web 应用 (*WebApp1*) 以便将两个项目中的命名空间匹配。 具有相同的命名空间，可以更轻松地将代码在两个项目之间复制。 您必须在与上一个项目以使用相同的名称不同的目录中创建此项目。

![“新建项目”对话框](mvc/_static/new_core.png)

![新建 ASP.NET Web 应用程序对话框：在 ASP.NET Core 模板面板中选择的空项目模板](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *可选：* 创建新的 ASP.NET Core 应用程序使用*Web 应用程序*项目模板。 将项目命名*WebApp1*，再选择身份验证选项的**单个用户帐户**。 重命名此应用*FullAspNetCore*。 在转换过程中创建此项目为您节省时间。 您可以看一下若要查看最终结果，或将代码复制到转换项目模板生成代码。 如果您遇到与模板生成项目进行比较的转换步骤，该技术还有助于。

## <a name="configure-the-site-to-use-mvc"></a>将站点配置为使用 MVC

::: moniker range=">= aspnetcore-2.1"

* 当面向.NET Core [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)默认情况下引用。 此包包含常用包的包 MVC 应用程序。 如果面向.NET Framework，包引用必须单独列出项目文件中。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* 当面向.NET Core [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)默认情况下引用。 此包包含常用包的包 MVC 应用程序。 如果面向.NET Framework，包引用必须单独列出项目文件中。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* 当目标.NET Core 或.NET Framework，包通常用于 MVC 应用程序列出包单独项目文件中。

::: moniker-end

`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core MVC 框架。 `Microsoft.AspNetCore.StaticFiles` 是静态文件处理程序。 ASP.NET Core 运行时是一个模块化，和中，你必须显式选择要为静态文件服务 (请参阅[静态文件](xref:fundamentals/static-files))。

* 打开*Startup.cs*文件，并更改代码以匹配以下内容：

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles`扩展方法将添加静态文件处理程序。 正如前面提到的是模块化，ASP.NET 运行时，你必须显式选择中提供静态文件。 `UseMvc`扩展方法将添加路由。 有关详细信息，请参阅[应用程序启动](xref:fundamentals/startup)并[路由](xref:fundamentals/routing)。

## <a name="add-a-controller-and-view"></a>添加控制器和视图

在本部分中，您将添加一个最小控制器和视图，以用作占位符的 ASP.NET MVC 控制器和视图将在下一节中迁移。

* 添加*控制器*文件夹。

* 添加**控制器类**名为*HomeController.cs*到*控制器*文件夹。

![“添加新项”对话框](mvc/_static/add_mvc_ctl.png)

* 添加*视图*文件夹。

* 添加*Views/Home*文件夹。

* 添加**Razor 视图**名为*Index.cshtml*到*Views/Home*文件夹。

![“添加新项”对话框](mvc/_static/view.png)

项目结构如下所示：

![显示文件和文件夹的 WebApp1 的解决方案资源管理器](mvc/_static/project-structure-controller-view.png)

内容替换为*Views/Home/Index.cshtml*具有以下文件：

```html
<h1>Hello world!</h1>
```

运行应用。

![在 Microsoft Edge 中打开 web 应用](mvc/_static/hello-world.png)

请参阅[控制器](xref:mvc/controllers/actions)并[视图](xref:mvc/views/overview)有关详细信息。

现在，我们已最小的工作 ASP.NET Core 项目，我们可以开始从 ASP.NET MVC 项目迁移功能。 我们需要移动以下：

* 客户端的内容 （CSS、 字体和脚本）

* 控制器

* 视图

* 模型

* 捆绑

* 筛选器

* 输入/输出的日志，标识 （这是在下一步的教程。）

## <a name="controllers-and-views"></a>控制器和视图

* 复制每个方法从 ASP.NET MVC`HomeController`对新`HomeController`。 请注意，在 ASP.NET MVC 内置模板的控制器操作方法的返回类型[ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); 在 ASP.NET Core MVC，操作方法返回`IActionResult`相反。 `ActionResult` 实现`IActionResult`，因此无需更改您的操作方法的返回类型。

* 复制*About.cshtml*， *Contact.cshtml*，和*Index.cshtml* Razor 视图文件从 ASP.NET MVC 项目添加到 ASP.NET Core 项目。

* 运行 ASP.NET Core 应用和测试每个方法。 我们还没有迁移样式的布局文件，因此呈现的视图仅包含在视图文件中的内容。 不会有的布局文件生成链接`About`并`Contact`视图，因此您必须调用，从浏览器 (替换**4492**与项目中使用的端口号)。

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![联系信息页](mvc/_static/contact-page.png)

请注意，没有样式设置和菜单项。 此问题将在下一部分得以解决。

## <a name="static-content"></a>静态内容

在以前版本的 ASP.NET MVC 中，静态内容托管从 web 项目的根目录和服务器端文件混合。 在 ASP.NET Core 静态内容承载于*wwwroot*文件夹。 你将想要复制的静态内容从旧 ASP.NET MVC 应用程序到*wwwroot* ASP.NET Core 项目文件夹中的。 在此示例转换：

* 复制*favicon.ico*文件从旧的 MVC 项目到*wwwroot* ASP.NET Core 项目文件夹中的。

旧 ASP.NET MVC 项目使用[Bootstrap](https://getbootstrap.com/) Bootstrap 文件在其样式以及存储区*内容*并*脚本*文件夹。 模板，用于生成旧的 ASP.NET MVC 项目，引用布局文件中的 Bootstrap (*Views/Shared/_Layout.cshtml*)。 无法复制*bootstrap.js*并*bootstrap.css*文件从 ASP.NET MVC 项目到*wwwroot*新项目文件夹中的。 相反，我们将添加对 Bootstrap 的支持 （和其他客户端库），它在下一节中使用 Cdn。

## <a name="migrate-the-layout-file"></a>将布局文件中迁移

* 复制 *_ViewStart.cshtml*文件从旧的 ASP.NET MVC 项目*视图*文件夹导入到 ASP.NET Core项目*视图*文件夹。 *_ViewStart.cshtml*文件未更改 ASP.NET Core mvc。

* 创建*Views/Shared*文件夹。

* *可选：* 复制 *_ViewImports.cshtml*从*FullAspNetCore* MVC 项目*视图*到 ASP.NET Core 项目的文件夹*视图*文件夹。 中的任何命名空间声明中删除 *_ViewImports.cshtml*文件。 *_ViewImports.cshtml*文件的所有视图文件提供了命名空间和带来[标记帮助程序](xref:mvc/views/tag-helpers/intro)。 新的布局文件中使用标记帮助程序。 *_ViewImports.cshtml*文件是用于 ASP.NET Core 新功能。

* 复制 *_Layout.cshtml*文件从旧的 ASP.NET MVC 项目*视图/共享*文件夹导入到 ASP.NET Core 项目*视图/共享*文件夹。

打开 *_Layout.cshtml*文件，然后进行以下更改 （如下所示的已完成的代码）：

* 替换`@Styles.Render("~/Content/css")`与`<link>`元素来加载*bootstrap.css* （见下文）。

* 删除 `@Scripts.Render("~/bundles/modernizr")`。

* 注释掉`@Html.Partial("_LoginPartial")`行 (括在行的`@*...*@`)。 有关详细信息请参阅[迁移身份验证和标识到 ASP.NET Core](xref:migration/identity)

* 替换`@Scripts.Render("~/bundles/jquery")`与`<script>`元素 （见下文）。

* 替换`@Scripts.Render("~/bundles/bootstrap")`与`<script>`元素 （见下文）。

Bootstrap CSS 包含替换标记：

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

JQuery 和 Bootstrap JavaScript 包含的替换标记：

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

已更新 *_Layout.cshtml*文件如下所示：

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

在浏览器中查看该站点。 它应现在正确加载，以就地预期样式。

* *可选：* 您可能想要尝试使用新的布局文件。 对于此项目中，你可以复制中的布局文件*FullAspNetCore*项目。 新的布局文件使用[标记帮助程序](xref:mvc/views/tag-helpers/intro)并且具有其他改进。

## <a name="configure-bundling-and-minification"></a>配置捆绑和缩减

有关如何配置绑定和缩减的信息，请参阅[捆绑和缩减](../client-side/bundling-and-minification.md)。

## <a name="solve-http-500-errors"></a>解决 HTTP 500 错误

有许多可能会导致 HTTP 500 错误消息包含没有关于此问题的源的信息的问题。 例如，如果*views/_viewimports.cshtml*文件包含在项目中不存在的命名空间，您将收到 HTTP 500 错误。 默认情况下，在 ASP.NET Core 应用中，`UseDeveloperExceptionPage`扩展添加到`IApplicationBuilder`和执行在配置后*开发*。 这将在下面的代码中详细说明：

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core 将在 web 应用程序中未经处理的异常转换为 HTTP 500 错误响应。 通常情况下，错误详细信息不包括在这些响应来防止泄露有关服务器的潜在敏感信息。 请参阅**使用开发人员异常页**中[处理错误](../fundamentals/error-handling.md)有关详细信息。

## <a name="additional-resources"></a>其他资源

* <xref:razor-components/index>
* <xref:mvc/views/tag-helpers/intro>
