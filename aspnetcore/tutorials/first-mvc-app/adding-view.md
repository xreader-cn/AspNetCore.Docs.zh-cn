---
title: 将视图添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 将视图添加到简单的 ASP.NET Core MVC 应用
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 1c29b59f9306774316ff37eeb57cc441fe5c7370
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820078"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>将视图添加到 ASP.NET Core MVC 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

在本部分中，将修改 `HelloWorldController` 类，进而使用 [Razor](xref:mvc/views/razor) 视图文件来顺利封装为客户端生成 HTML 响应的过程。

使用 Razor 创建视图模板文件。 基于 Razor 的模板具有“.cshtml”文件扩展名  。 它们提供了一种巧妙的方法来使用 C# 创建 HTML 输出。

当前，`Index` 方法返回带有在控制器类中硬编码的消息的字符串。 在 `HelloWorldController` 类中，将 `Index` 方法替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

上面的代码调用控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。 它使用视图模板来生成 HTML 响应。 控制器方法（亦称为“操作方法”  ，如上面的 `Index` 方法）通常返回 <xref:Microsoft.AspNetCore.Mvc.IActionResult>（或派生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的类），而不是 `string` 等类型。

## <a name="add-a-view"></a>添加视图

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   

* 右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新项”。  

* 在“添加新项 - MvcMovie”对话框中 

  * 在右上角的搜索框中，输入“视图” 

  * 选择“Razor 视图” 

  * 保持“名称”框的值：Index.cshtml   。

  * 选择“添加” 

![“添加新项”对话框](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

为 `HelloWorldController` 添加 `Index` 视图。

* 添加一个名为“Views/HelloWorld”的新文件夹。 
* 向 Views/HelloWorld 文件夹添加名为“Index.cshtml”的新文件。  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   
* 右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新文件”   。
* 在“新建文件”对话框中  ：

  * 在左窗格中，选择“Web”  。
  * 在中间窗格中，选择“空 HTML 文件”  。
  * 在“名称”框中键入“Index.cshtml”   。
  * 选择“新建”  。

![“添加新项”对话框](adding-view/_static/add_view_mac.png)

---

使用以下内容替换 Razor 视图文件 Views/HelloWorld/Index.cshtml 的内容  ：

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

导航到 `https://localhost:{PORT}/HelloWorld`。 `HelloWorldController` 中的 `Index` 方法作用不大；它运行 `return View();` 语句，指定此方法应使用视图模板文件来呈现对浏览器的响应。 由于未指定视图模板文件名称，MVC 默认使用默认视图文件。 默认视图文件具有与方法 (`Index`) 相同的名称，因此在 /Views/HelloWorld/Index.cshtml  中使用。 下面图片显示了视图中硬编码的 字符串“Hello from our View Template!”

![浏览器窗口](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>更改视图和布局页面

选择菜单链接（“MvcMovie”、“首页”和“隐私”）    。 每页显示相同的菜单布局。 菜单布局是在 Views/Shared/_Layout.cshtml 文件中实现的  。 打开 Views/Shared/_Layout.cshtml 文件  。

[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。 查找 `@RenderBody()` 行。 `RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中  。 例如，如果选择“隐私”链接，Views/Home/Privacy.cshtml 视图将在 `RenderBody` 方法中呈现   。

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>更改布局文件中的标题、页脚和菜单链接

将 Views\Shared\_Layout.cshtml  文件的内容替换为以下标记。 突出显示所作更改：

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

上述标记进行以下更改：

* `MvcMovie` 更改为 `Movie App` 3 次。
* 定位点元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 更改为 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。

在前面的标记中，省略了 `asp-area=""` [定位点标记帮助程序属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)和属性值，因为此应用未使用[区域](xref:mvc/controllers/areas)。

**说明**：`Movies` 控制器尚未实现。 此时，`Movie App` 链接不起作用。

保存更改并选择“隐私”链接  。 请注意浏览器选项卡上的标题现在显示的是“隐私策略 - 电影应用”  ，而不是“隐私策略 - Mvc 电影”  ：

![“隐私”选项卡](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

选择“主页”链接，请注意，标题和定位文本还会显示“电影应用”   。 我们能够在布局模板中进行一次更改，让网站上的所有页面都反映新的链接文本和新标题。

检查 Views/_ViewStart.cshtml 文件  ：

```HTML
@{
    Layout = "_Layout";
}
```

Views/_ViewStart.cshtml 文件将 Views/Shared/_Layout.cshtml 文件引入到每个视图中   。 可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。

更改 Views/HelloWorld/Index.cshtml 视图文件的 `<h2>` 元素  ：

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

稍稍对标题和 `<h2>` 元素进行一些更改，这样可以看出哪一段代码更改了显示。

上述代码中的 `ViewData["Title"] = "Movie List";` 将 `ViewData` 字典的 `Title` 属性设置为“Movie List”。 `Title` 属性在布局页面中的 `<title>` HTML 元素中使用：

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

保存更改并导航到 `https://localhost:{PORT}/HelloWorld`。 请注意，浏览器标题、主标题和辅助标题已更改。 （如果没有在浏览器中看到更改，则可能正在查看缓存的内容。 在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。）浏览器标题是使用我们在 Index.cshtml 视图模板中设置的 `ViewData["Title"]` 以及在布局文件中添加的额外“ - Movie App”创建的  。

Index.cshtml 视图模板中的内容与 Views/Shared/_Layout.cshtml 视图模板合并   。 单个 HTML 响应将发送到浏览器。 凭借布局模板可以轻松地对应用中所有页面进行更改。 若要了解更多信息，请参阅[布局](xref:mvc/views/layout)。

![电影列表视图](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

但我们这一点点“数据”（在此示例中为“Hello from our View Template!” 消息）是硬编码的。 MVC 应用程序有一个“V”（视图），而你已有一个“C”（控制器），但还没有“M”（模型）。

## <a name="passing-data-from-the-controller-to-the-view"></a>将数据从控制器传递给视图

控制器操作会被调用以响应传入的 URL 请求。 控制器类是编写处理传入浏览器请求的代码的地方。 控制器从数据源检索数据，并决定将哪些类型的响应发送回浏览器。 可以从控制器使用视图模板来生成并格式化对浏览器的 HTML 响应。

控制器负责提供所需的数据，使视图模板能够呈现响应。 最佳做法：视图模板不应该直接执行业务逻辑或与数据库进行交互  。 相反，视图模板应仅使用由控制器提供给它的数据。 保持此“分离关注点”有助于保持代码干净以及可测试性和可维护性。

目前，`HelloWorldController` 类中的 `Welcome` 方法采用 `name` 和 `ID` 参数，然后将值直接输出到浏览器。 应将控制器更改为使用视图模板，而不是使控制器将此响应呈现为字符串。 视图模板会生成动态响应，这意味着必须将适当的数据位从控制器传递给视图以生成响应。 为此，可以让控制器将视图模板所需的动态数据（参数）放置在视图模板稍后可以访问的 `ViewData` 字典中。

在 HelloWorldController.cs 中，更改 `Welcome` 方法以将 `Message` 和 `NumTimes` 值添加到 `ViewData` 字典  。 `ViewData` 字典是一个动态对象，这意味着可以使用任何类型；只有将内容放在其中后 `ViewData` 对象才具有定义的属性。 [MVC 模型绑定系统](xref:mvc/models/model-binding)自动将命名参数（`name` 和 `numTimes`）从地址栏中的查询字符串映射到方法中的参数。 完整的 HelloWorldController.cs 文件如下所示  ：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` 字典对象包含将传递给视图的数据。

创建一个名为 Views/HelloWorld/Welcome.cshtml 的 Welcome 视图模板  。

在 Welcome.cshtml 视图模板中创建一个循环，显示“Hello” `NumTimes`  。 使用以下内容替换 Views/HelloWorld/Welcome.cshtml 的内容  ：

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

保存更改并浏览到以下 URL：

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

数据取自 URL，并传递给使用 [MVC 模型绑定器](xref:mvc/models/model-binding)的控制器。 控制器将数据打包到 `ViewData` 字典中，并将该对象传递给视图。 然后，视图将数据作为 HTML 呈现给浏览器。

![“隐私”视图，显示了 Welcome 标签以及四个“Hello Rick”短语](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

在上面的示例中，我们使用 `ViewData` 字典将数据从控制器传递给视图。 稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。 传递数据的视图模型方法通常比 `ViewData` 字典方法更为优先。 有关详细信息，请参阅[何时使用 ViewBag、ViewData 或 TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)。

在下一个教程中，将创建电影数据库。

> [!div class="step-by-step"]
> [上一页](adding-controller.md)
> [下一页](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在本部分中，将修改 `HelloWorldController` 类，进而使用 [Razor](xref:mvc/views/razor) 视图文件来顺利封装为客户端生成 HTML 响应的过程。

使用 Razor 创建视图模板文件。 基于 Razor 的模板具有“.cshtml”文件扩展名  。 它们提供了一种巧妙的方法来使用 C# 创建 HTML 输出。

当前，`Index` 方法返回带有在控制器类中硬编码的消息的字符串。 在 `HelloWorldController` 类中，将 `Index` 方法替换为以下代码：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

上面的代码调用控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。 它使用视图模板来生成 HTML 响应。 控制器方法（亦称为“操作方法”  ，如上面的 `Index` 方法）通常返回 <xref:Microsoft.AspNetCore.Mvc.IActionResult>（或派生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的类），而不是 `string` 等类型。

## <a name="add-a-view"></a>添加视图

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   

* 右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新项”。  

* 在“添加新项 - MvcMovie”对话框中 

  * 在右上角的搜索框中，输入“视图” 

  * 选择“Razor 视图” 

  * 保持“名称”框的值：Index.cshtml   。

  * 选择“添加” 

![“添加新项”对话框](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

为 `HelloWorldController` 添加 `Index` 视图。

* 添加一个名为“Views/HelloWorld”的新文件夹。 
* 向 Views/HelloWorld 文件夹添加名为“Index.cshtml”的新文件。  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   
* 右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新文件”   。
* 在“新建文件”对话框中  ：

  * 在左窗格中，选择“Web”  。
  * 在中间窗格中，选择“空 HTML 文件”  。
  * 在“名称”框中键入“Index.cshtml”   。
  * 选择“新建”  。

![“添加新项”对话框](adding-view/_static/add_view_mac.png)

---

使用以下内容替换 Razor 视图文件 Views/HelloWorld/Index.cshtml 的内容  ：

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

导航到 `https://localhost:{PORT}/HelloWorld`。 `HelloWorldController` 中的 `Index` 方法作用不大；它运行 `return View();` 语句，指定此方法应使用视图模板文件来呈现对浏览器的响应。 由于未指定视图模板文件名称，MVC 默认使用默认视图文件。 默认视图文件具有与方法 (`Index`) 相同的名称，因此在 /Views/HelloWorld/Index.cshtml  中使用。 下面图片显示了视图中硬编码的 字符串“Hello from our View Template!”

![浏览器窗口](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>更改视图和布局页面

选择菜单链接（“MvcMovie”、“首页”和“隐私”）    。 每页显示相同的菜单布局。 菜单布局是在 Views/Shared/_Layout.cshtml 文件中实现的  。 打开 Views/Shared/_Layout.cshtml 文件  。

[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。 查找 `@RenderBody()` 行。 `RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中  。 例如，如果选择“隐私”链接，Views/Home/Privacy.cshtml 视图将在 `RenderBody` 方法中呈现   。

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>更改布局文件中的标题、页脚和菜单链接

* 在标题和页脚元素中，将 `MvcMovie` 更改为 `Movie App`。
* 将定位点元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 更改为 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。

下列标记显示了突出显示的更改：

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

在前面的标记中，省略了 `asp-area` [定位点标记帮助程序属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)，因为此应用未使用[区域](xref:mvc/controllers/areas)。

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**说明**：`Movies` 控制器尚未实现。 此时，`Movie App` 链接不起作用。

保存更改并选择“隐私”链接  。 请注意浏览器选项卡上的标题现在显示的是“隐私策略 - 电影应用”  ，而不是“隐私策略 - Mvc 电影”  ：

![“隐私”选项卡](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

选择“主页”链接，请注意，标题和定位文本还会显示“电影应用”   。 我们能够在布局模板中进行一次更改，让网站上的所有页面都反映新的链接文本和新标题。

检查 Views/_ViewStart.cshtml 文件  ：

```HTML
@{
    Layout = "_Layout";
}
```

Views/_ViewStart.cshtml 文件将 Views/Shared/_Layout.cshtml 文件引入到每个视图中   。 可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。

更改 Views/HelloWorld/Index.cshtml 视图文件的 `<h2>` 元素  ：

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

稍稍对标题和 `<h2>` 元素进行一些更改，这样可以看出哪一段代码更改了显示。

上述代码中的 `ViewData["Title"] = "Movie List";` 将 `ViewData` 字典的 `Title` 属性设置为“Movie List”。 `Title` 属性在布局页面中的 `<title>` HTML 元素中使用：

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

保存更改并导航到 `https://localhost:{PORT}/HelloWorld`。 请注意，浏览器标题、主标题和辅助标题已更改。 （如果没有在浏览器中看到更改，则可能正在查看缓存的内容。 在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。）浏览器标题是使用我们在 Index.cshtml 视图模板中设置的 `ViewData["Title"]` 以及在布局文件中添加的额外“ - Movie App”创建的  。

还要注意，Index.cshtml 视图模板中的内容是如何与 Views/Shared/_Layout.cshtml 视图模板合并的，并且注意单个 HTML 响应被发送到了浏览器   。 凭借布局模板可以很容易地对应用程序中所有页面进行更改。 若要了解更多信息，请参阅[布局](xref:mvc/views/layout)。

![电影列表视图](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

但我们这一点点“数据”（在此示例中为“Hello from our View Template!” 消息）是硬编码的。 MVC 应用程序有一个“V”（视图），而你已有一个“C”（控制器），但还没有“M”（模型）。

## <a name="passing-data-from-the-controller-to-the-view"></a>将数据从控制器传递给视图

控制器操作会被调用以响应传入的 URL 请求。 控制器类是编写处理传入浏览器请求的代码的地方。 控制器从数据源检索数据，并决定将哪些类型的响应发送回浏览器。 可以从控制器使用视图模板来生成并格式化对浏览器的 HTML 响应。

控制器负责提供所需的数据，使视图模板能够呈现响应。 最佳做法：视图模板不应该直接执行业务逻辑或与数据库进行交互  。 相反，视图模板应仅使用由控制器提供给它的数据。 保持此“分离关注点”有助于保持代码干净以及可测试性和可维护性。

目前，`HelloWorldController` 类中的 `Welcome` 方法采用 `name` 和 `ID` 参数，然后将值直接输出到浏览器。 应将控制器更改为使用视图模板，而不是使控制器将此响应呈现为字符串。 视图模板会生成动态响应，这意味着必须将适当的数据位从控制器传递给视图以生成响应。 为此，可以让控制器将视图模板所需的动态数据（参数）放置在视图模板稍后可以访问的 `ViewData` 字典中。

在 HelloWorldController.cs 中，更改 `Welcome` 方法以将 `Message` 和 `NumTimes` 值添加到 `ViewData` 字典  。 `ViewData` 字典是一个动态对象，这意味着可以使用任何类型；只有将内容放在其中后 `ViewData` 对象才具有定义的属性。 [MVC 模型绑定系统](xref:mvc/models/model-binding)自动将命名参数（`name` 和 `numTimes`）从地址栏中的查询字符串映射到方法中的参数。 完整的 HelloWorldController.cs 文件如下所示  ：

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` 字典对象包含将传递给视图的数据。

创建一个名为 Views/HelloWorld/Welcome.cshtml 的 Welcome 视图模板  。

在 Welcome.cshtml 视图模板中创建一个循环，显示“Hello” `NumTimes`  。 使用以下内容替换 Views/HelloWorld/Welcome.cshtml 的内容  ：

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

保存更改并浏览到以下 URL：

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

数据取自 URL，并传递给使用 [MVC 模型绑定器](xref:mvc/models/model-binding)的控制器。 控制器将数据打包到 `ViewData` 字典中，并将该对象传递给视图。 然后，视图将数据作为 HTML 呈现给浏览器。

![“隐私”视图，显示了 Welcome 标签以及四个“Hello Rick”短语](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

在上面的示例中，我们使用 `ViewData` 字典将数据从控制器传递给视图。 稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。 传递数据的视图模型方法通常比 `ViewData` 字典方法更为优先。 有关详细信息，请参阅[何时使用 ViewBag、ViewData 或 TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)。

在下一个教程中，将创建电影数据库。

> [!div class="step-by-step"]
> [上一页](adding-controller.md)
> [下一页](adding-model.md)

::: moniker-end
