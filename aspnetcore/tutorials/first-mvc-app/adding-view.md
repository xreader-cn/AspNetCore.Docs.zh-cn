---
title: 将视图添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 将视图添加到简单的 ASP.NET Core MVC 应用
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: a25233968f115c6e3a214d97cf2ca5ab81df8d83
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870423"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="247e2-103">将视图添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="247e2-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="247e2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="247e2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="247e2-105">在本部分中，将修改 `HelloWorldController` 类，进而使用 [Razor](xref:mvc/views/razor) 视图文件来顺利封装为客户端生成 HTML 响应的过程。</span><span class="sxs-lookup"><span data-stu-id="247e2-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="247e2-106">使用 Razor 创建视图模板文件。</span><span class="sxs-lookup"><span data-stu-id="247e2-106">You create a view template file using Razor.</span></span> <span data-ttu-id="247e2-107">基于 Razor 的模板具有“.cshtml”文件扩展名  。</span><span class="sxs-lookup"><span data-stu-id="247e2-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="247e2-108">它们提供了一种巧妙的方法来使用 C# 创建 HTML 输出。</span><span class="sxs-lookup"><span data-stu-id="247e2-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="247e2-109">当前，`Index` 方法返回带有在控制器类中硬编码的消息的字符串。</span><span class="sxs-lookup"><span data-stu-id="247e2-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="247e2-110">在 `HelloWorldController` 类中，将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="247e2-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="247e2-111">上面的代码调用控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="247e2-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="247e2-112">它使用视图模板来生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="247e2-113">控制器方法（亦称为“操作方法”  ，如上面的 `Index` 方法）通常返回 <xref:Microsoft.AspNetCore.Mvc.IActionResult>（或派生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的类），而不是 `string` 等类型。</span><span class="sxs-lookup"><span data-stu-id="247e2-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="247e2-114">添加视图</span><span class="sxs-lookup"><span data-stu-id="247e2-114">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="247e2-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="247e2-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="247e2-116">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   </span><span class="sxs-lookup"><span data-stu-id="247e2-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="247e2-117">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新项”。  </span><span class="sxs-lookup"><span data-stu-id="247e2-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="247e2-118">在“添加新项 - MvcMovie”对话框中 </span><span class="sxs-lookup"><span data-stu-id="247e2-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="247e2-119">在右上角的搜索框中，输入“视图” </span><span class="sxs-lookup"><span data-stu-id="247e2-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="247e2-120">选择“Razor 视图” </span><span class="sxs-lookup"><span data-stu-id="247e2-120">Select **Razor View**</span></span>

  * <span data-ttu-id="247e2-121">保持“名称”框的值：Index.cshtml   。</span><span class="sxs-lookup"><span data-stu-id="247e2-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="247e2-122">选择“添加” </span><span class="sxs-lookup"><span data-stu-id="247e2-122">Select **Add**</span></span>

![“添加新项”对话框](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="247e2-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="247e2-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="247e2-125">为 `HelloWorldController` 添加 `Index` 视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="247e2-126">添加一个名为“Views/HelloWorld”的新文件夹。 </span><span class="sxs-lookup"><span data-stu-id="247e2-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="247e2-127">向 Views/HelloWorld 文件夹添加名为“Index.cshtml”的新文件。  </span><span class="sxs-lookup"><span data-stu-id="247e2-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="247e2-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="247e2-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="247e2-129">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   </span><span class="sxs-lookup"><span data-stu-id="247e2-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="247e2-130">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新文件”   。</span><span class="sxs-lookup"><span data-stu-id="247e2-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="247e2-131">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="247e2-132">在左侧窗格中，选择“ASP.NET Core”  。</span><span class="sxs-lookup"><span data-stu-id="247e2-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="247e2-133">在中间窗格中，选择“MVC 视图页面”  。</span><span class="sxs-lookup"><span data-stu-id="247e2-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="247e2-134">在“名称”框中键入“Index”   。</span><span class="sxs-lookup"><span data-stu-id="247e2-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="247e2-135">选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="247e2-135">Select **New**.</span></span>

![“添加新项”对话框](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="247e2-137">使用以下内容替换 Razor 视图文件 Views/HelloWorld/Index.cshtml 的内容  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="247e2-138">导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="247e2-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="247e2-139">`HelloWorldController` 中的 `Index` 方法作用不大；它运行 `return View();` 语句，指定此方法应使用视图模板文件来呈现对浏览器的响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="247e2-140">由于未指定视图模板文件名称，MVC 默认使用默认视图文件。</span><span class="sxs-lookup"><span data-stu-id="247e2-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="247e2-141">默认视图文件具有与方法 (`Index`) 相同的名称，因此在 /Views/HelloWorld/Index.cshtml  中使用。</span><span class="sxs-lookup"><span data-stu-id="247e2-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="247e2-142">下面图片显示了视图中硬编码的</span><span class="sxs-lookup"><span data-stu-id="247e2-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="247e2-143">字符串“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="247e2-143">hard-coded in the view.</span></span>

![浏览器窗口](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="247e2-145">更改视图和布局页面</span><span class="sxs-lookup"><span data-stu-id="247e2-145">Change views and layout pages</span></span>

<span data-ttu-id="247e2-146">选择菜单链接（“MvcMovie”、“首页”和“隐私”）    。</span><span class="sxs-lookup"><span data-stu-id="247e2-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="247e2-147">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="247e2-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="247e2-148">菜单布局是在 Views/Shared/_Layout.cshtml 文件中实现的  。</span><span class="sxs-lookup"><span data-stu-id="247e2-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="247e2-149">打开 Views/Shared/_Layout.cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="247e2-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="247e2-150">[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="247e2-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="247e2-151">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="247e2-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="247e2-152">`RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中  。</span><span class="sxs-lookup"><span data-stu-id="247e2-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="247e2-153">例如，如果选择“隐私”链接，Views/Home/Privacy.cshtml 视图将在 `RenderBody` 方法中呈现   。</span><span class="sxs-lookup"><span data-stu-id="247e2-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="247e2-154">更改布局文件中的标题、页脚和菜单链接</span><span class="sxs-lookup"><span data-stu-id="247e2-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="247e2-155">将 Views/Shared/_Layout.cshtml 文件的内容替换为以下标记。 </span><span class="sxs-lookup"><span data-stu-id="247e2-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="247e2-156">突出显示所作更改：</span><span class="sxs-lookup"><span data-stu-id="247e2-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="247e2-157">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="247e2-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="247e2-158">`MvcMovie` 更改为 `Movie App` 3 次。</span><span class="sxs-lookup"><span data-stu-id="247e2-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="247e2-159">定位点元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 更改为 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="247e2-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="247e2-160">在前面的标记中，省略了 `asp-area=""` [定位点标记帮助程序属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)和属性值，因为此应用未使用[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="247e2-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="247e2-161">**说明**：`Movies` 控制器尚未实现。</span><span class="sxs-lookup"><span data-stu-id="247e2-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="247e2-162">此时，`Movie App` 链接不起作用。</span><span class="sxs-lookup"><span data-stu-id="247e2-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="247e2-163">保存更改并选择“隐私”链接  。</span><span class="sxs-lookup"><span data-stu-id="247e2-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="247e2-164">请注意浏览器选项卡上的标题现在显示的是“隐私策略 - 电影应用”  ，而不是“隐私策略 - Mvc 电影”  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![“隐私”选项卡](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="247e2-166">选择“主页”链接，请注意，标题和定位文本还会显示“电影应用”   。</span><span class="sxs-lookup"><span data-stu-id="247e2-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="247e2-167">我们能够在布局模板中进行一次更改，让网站上的所有页面都反映新的链接文本和新标题。</span><span class="sxs-lookup"><span data-stu-id="247e2-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="247e2-168">检查 Views/_ViewStart.cshtml 文件  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="247e2-169">Views/_ViewStart.cshtml 文件将 Views/Shared/_Layout.cshtml 文件引入到每个视图中   。</span><span class="sxs-lookup"><span data-stu-id="247e2-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="247e2-170">可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。</span><span class="sxs-lookup"><span data-stu-id="247e2-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="247e2-171">更改 Views/HelloWorld/Index.cshtml 视图文件的 `<h2>` 元素  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="247e2-172">稍稍对标题和 `<h2>` 元素进行一些更改，这样可以看出哪一段代码更改了显示。</span><span class="sxs-lookup"><span data-stu-id="247e2-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="247e2-173">上述代码中的 `ViewData["Title"] = "Movie List";` 将 `ViewData` 字典的 `Title` 属性设置为“Movie List”。</span><span class="sxs-lookup"><span data-stu-id="247e2-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="247e2-174">`Title` 属性在布局页面中的 `<title>` HTML 元素中使用：</span><span class="sxs-lookup"><span data-stu-id="247e2-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="247e2-175">保存更改并导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="247e2-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="247e2-176">请注意，浏览器标题、主标题和辅助标题已更改。</span><span class="sxs-lookup"><span data-stu-id="247e2-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="247e2-177">（如果没有在浏览器中看到更改，则可能正在查看缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="247e2-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="247e2-178">在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。）浏览器标题是使用我们在 Index.cshtml 视图模板中设置的 `ViewData["Title"]` 以及在布局文件中添加的额外“ - Movie App”创建的  。</span><span class="sxs-lookup"><span data-stu-id="247e2-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="247e2-179">Index.cshtml 视图模板中的内容与 Views/Shared/_Layout.cshtml 视图模板合并   。</span><span class="sxs-lookup"><span data-stu-id="247e2-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="247e2-180">单个 HTML 响应将发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="247e2-181">凭借布局模板可以轻松地对应用中所有页面进行更改。</span><span class="sxs-lookup"><span data-stu-id="247e2-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="247e2-182">若要了解更多信息，请参阅[布局](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="247e2-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![电影列表视图](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="247e2-184">但我们这一点点“数据”（在此示例中为“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="247e2-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="247e2-185">消息）是硬编码的。</span><span class="sxs-lookup"><span data-stu-id="247e2-185">message) is hard-coded, though.</span></span> <span data-ttu-id="247e2-186">MVC 应用程序有一个“V”（视图），而你已有一个“C”（控制器），但还没有“M”（模型）。</span><span class="sxs-lookup"><span data-stu-id="247e2-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="247e2-187">将数据从控制器传递给视图</span><span class="sxs-lookup"><span data-stu-id="247e2-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="247e2-188">控制器操作会被调用以响应传入的 URL 请求。</span><span class="sxs-lookup"><span data-stu-id="247e2-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="247e2-189">控制器类是编写处理传入浏览器请求的代码的地方。</span><span class="sxs-lookup"><span data-stu-id="247e2-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="247e2-190">控制器从数据源检索数据，并决定将哪些类型的响应发送回浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="247e2-191">可以从控制器使用视图模板来生成并格式化对浏览器的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="247e2-192">控制器负责提供所需的数据，使视图模板能够呈现响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="247e2-193">最佳做法：视图模板不应该直接执行业务逻辑或与数据库进行交互  。</span><span class="sxs-lookup"><span data-stu-id="247e2-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="247e2-194">相反，视图模板应仅使用由控制器提供给它的数据。</span><span class="sxs-lookup"><span data-stu-id="247e2-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="247e2-195">保持此“分离关注点”有助于保持代码干净以及可测试性和可维护性。</span><span class="sxs-lookup"><span data-stu-id="247e2-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="247e2-196">目前，`HelloWorldController` 类中的 `Welcome` 方法采用 `name` 和 `ID` 参数，然后将值直接输出到浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="247e2-197">应将控制器更改为使用视图模板，而不是使控制器将此响应呈现为字符串。</span><span class="sxs-lookup"><span data-stu-id="247e2-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="247e2-198">视图模板会生成动态响应，这意味着必须将适当的数据位从控制器传递给视图以生成响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="247e2-199">为此，可以让控制器将视图模板所需的动态数据（参数）放置在视图模板稍后可以访问的 `ViewData` 字典中。</span><span class="sxs-lookup"><span data-stu-id="247e2-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="247e2-200">在 HelloWorldController.cs 中，更改 `Welcome` 方法以将 `Message` 和 `NumTimes` 值添加到 `ViewData` 字典  。</span><span class="sxs-lookup"><span data-stu-id="247e2-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="247e2-201">`ViewData` 字典是一个动态对象，这意味着可以使用任何类型；只有将内容放在其中后 `ViewData` 对象才具有定义的属性。</span><span class="sxs-lookup"><span data-stu-id="247e2-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="247e2-202">[MVC 模型绑定系统](xref:mvc/models/model-binding)自动将命名参数（`name` 和 `numTimes`）从地址栏中的查询字符串映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="247e2-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="247e2-203">完整的 HelloWorldController.cs 文件如下所示  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="247e2-204">`ViewData` 字典对象包含将传递给视图的数据。</span><span class="sxs-lookup"><span data-stu-id="247e2-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="247e2-205">创建一个名为 Views/HelloWorld/Welcome.cshtml 的 Welcome 视图模板  。</span><span class="sxs-lookup"><span data-stu-id="247e2-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="247e2-206">在 Welcome.cshtml 视图模板中创建一个循环，显示“Hello” `NumTimes`  。</span><span class="sxs-lookup"><span data-stu-id="247e2-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="247e2-207">使用以下内容替换 Views/HelloWorld/Welcome.cshtml 的内容  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="247e2-208">保存更改并浏览到以下 URL：</span><span class="sxs-lookup"><span data-stu-id="247e2-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="247e2-209">数据取自 URL，并传递给使用 [MVC 模型绑定器](xref:mvc/models/model-binding)的控制器。</span><span class="sxs-lookup"><span data-stu-id="247e2-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="247e2-210">控制器将数据打包到 `ViewData` 字典中，并将该对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="247e2-211">然后，视图将数据作为 HTML 呈现给浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-211">The view then renders the data as HTML to the browser.</span></span>

![“隐私”视图，显示了 Welcome 标签以及四个“Hello Rick”短语](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="247e2-213">在上面的示例中，我们使用 `ViewData` 字典将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="247e2-214">稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="247e2-215">传递数据的视图模型方法通常比 `ViewData` 字典方法更为优先。</span><span class="sxs-lookup"><span data-stu-id="247e2-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="247e2-216">有关详细信息，请参阅[何时使用 ViewBag、ViewData 或 TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)。</span><span class="sxs-lookup"><span data-stu-id="247e2-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="247e2-217">在下一个教程中，将创建电影数据库。</span><span class="sxs-lookup"><span data-stu-id="247e2-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="247e2-218">[上一页](adding-controller.md)
> [下一页](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="247e2-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="247e2-219">在本部分中，将修改 `HelloWorldController` 类，进而使用 [Razor](xref:mvc/views/razor) 视图文件来顺利封装为客户端生成 HTML 响应的过程。</span><span class="sxs-lookup"><span data-stu-id="247e2-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="247e2-220">使用 Razor 创建视图模板文件。</span><span class="sxs-lookup"><span data-stu-id="247e2-220">You create a view template file using Razor.</span></span> <span data-ttu-id="247e2-221">基于 Razor 的模板具有“.cshtml”文件扩展名  。</span><span class="sxs-lookup"><span data-stu-id="247e2-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="247e2-222">它们提供了一种巧妙的方法来使用 C# 创建 HTML 输出。</span><span class="sxs-lookup"><span data-stu-id="247e2-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="247e2-223">当前，`Index` 方法返回带有在控制器类中硬编码的消息的字符串。</span><span class="sxs-lookup"><span data-stu-id="247e2-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="247e2-224">在 `HelloWorldController` 类中，将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="247e2-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="247e2-225">上面的代码调用控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="247e2-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="247e2-226">它使用视图模板来生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="247e2-227">控制器方法（亦称为“操作方法”  ，如上面的 `Index` 方法）通常返回 <xref:Microsoft.AspNetCore.Mvc.IActionResult>（或派生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的类），而不是 `string` 等类型。</span><span class="sxs-lookup"><span data-stu-id="247e2-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="247e2-228">添加视图</span><span class="sxs-lookup"><span data-stu-id="247e2-228">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="247e2-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="247e2-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="247e2-230">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   </span><span class="sxs-lookup"><span data-stu-id="247e2-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="247e2-231">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新项”。  </span><span class="sxs-lookup"><span data-stu-id="247e2-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="247e2-232">在“添加新项 - MvcMovie”对话框中 </span><span class="sxs-lookup"><span data-stu-id="247e2-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="247e2-233">在右上角的搜索框中，输入“视图” </span><span class="sxs-lookup"><span data-stu-id="247e2-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="247e2-234">选择“Razor 视图” </span><span class="sxs-lookup"><span data-stu-id="247e2-234">Select **Razor View**</span></span>

  * <span data-ttu-id="247e2-235">保持“名称”框的值：Index.cshtml   。</span><span class="sxs-lookup"><span data-stu-id="247e2-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="247e2-236">选择“添加” </span><span class="sxs-lookup"><span data-stu-id="247e2-236">Select **Add**</span></span>

![“添加新项”对话框](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="247e2-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="247e2-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="247e2-239">为 `HelloWorldController` 添加 `Index` 视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="247e2-240">添加一个名为“Views/HelloWorld”的新文件夹。 </span><span class="sxs-lookup"><span data-stu-id="247e2-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="247e2-241">向 Views/HelloWorld 文件夹添加名为“Index.cshtml”的新文件。  </span><span class="sxs-lookup"><span data-stu-id="247e2-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="247e2-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="247e2-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="247e2-243">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。   </span><span class="sxs-lookup"><span data-stu-id="247e2-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="247e2-244">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新文件”   。</span><span class="sxs-lookup"><span data-stu-id="247e2-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="247e2-245">在“新建文件”对话框中  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="247e2-246">在左窗格中，选择“Web”  。</span><span class="sxs-lookup"><span data-stu-id="247e2-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="247e2-247">在中间窗格中，选择“空 HTML 文件”  。</span><span class="sxs-lookup"><span data-stu-id="247e2-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="247e2-248">在“名称”框中键入“Index.cshtml”   。</span><span class="sxs-lookup"><span data-stu-id="247e2-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="247e2-249">选择“新建”  。</span><span class="sxs-lookup"><span data-stu-id="247e2-249">Select **New**.</span></span>

![“添加新项”对话框](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="247e2-251">使用以下内容替换 Razor 视图文件 Views/HelloWorld/Index.cshtml 的内容  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="247e2-252">导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="247e2-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="247e2-253">`HelloWorldController` 中的 `Index` 方法作用不大；它运行 `return View();` 语句，指定此方法应使用视图模板文件来呈现对浏览器的响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="247e2-254">由于未指定视图模板文件名称，MVC 默认使用默认视图文件。</span><span class="sxs-lookup"><span data-stu-id="247e2-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="247e2-255">默认视图文件具有与方法 (`Index`) 相同的名称，因此在 /Views/HelloWorld/Index.cshtml  中使用。</span><span class="sxs-lookup"><span data-stu-id="247e2-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="247e2-256">下面图片显示了视图中硬编码的</span><span class="sxs-lookup"><span data-stu-id="247e2-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="247e2-257">字符串“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="247e2-257">hard-coded in the view.</span></span>

![浏览器窗口](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="247e2-259">更改视图和布局页面</span><span class="sxs-lookup"><span data-stu-id="247e2-259">Change views and layout pages</span></span>

<span data-ttu-id="247e2-260">选择菜单链接（“MvcMovie”、“首页”和“隐私”）    。</span><span class="sxs-lookup"><span data-stu-id="247e2-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="247e2-261">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="247e2-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="247e2-262">菜单布局是在 Views/Shared/_Layout.cshtml 文件中实现的  。</span><span class="sxs-lookup"><span data-stu-id="247e2-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="247e2-263">打开 Views/Shared/_Layout.cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="247e2-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="247e2-264">[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="247e2-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="247e2-265">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="247e2-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="247e2-266">`RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中  。</span><span class="sxs-lookup"><span data-stu-id="247e2-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="247e2-267">例如，如果选择“隐私”链接，Views/Home/Privacy.cshtml 视图将在 `RenderBody` 方法中呈现   。</span><span class="sxs-lookup"><span data-stu-id="247e2-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="247e2-268">更改布局文件中的标题、页脚和菜单链接</span><span class="sxs-lookup"><span data-stu-id="247e2-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="247e2-269">在标题和页脚元素中，将 `MvcMovie` 更改为 `Movie App`。</span><span class="sxs-lookup"><span data-stu-id="247e2-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="247e2-270">将定位点元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 更改为 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="247e2-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="247e2-271">下列标记显示了突出显示的更改：</span><span class="sxs-lookup"><span data-stu-id="247e2-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="247e2-272">在前面的标记中，省略了 `asp-area` [定位点标记帮助程序属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)，因为此应用未使用[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="247e2-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="247e2-273">**说明**：`Movies` 控制器尚未实现。</span><span class="sxs-lookup"><span data-stu-id="247e2-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="247e2-274">此时，`Movie App` 链接不起作用。</span><span class="sxs-lookup"><span data-stu-id="247e2-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="247e2-275">保存更改并选择“隐私”链接  。</span><span class="sxs-lookup"><span data-stu-id="247e2-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="247e2-276">请注意浏览器选项卡上的标题现在显示的是“隐私策略 - 电影应用”  ，而不是“隐私策略 - Mvc 电影”  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![“隐私”选项卡](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="247e2-278">选择“主页”链接，请注意，标题和定位文本还会显示“电影应用”   。</span><span class="sxs-lookup"><span data-stu-id="247e2-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="247e2-279">我们能够在布局模板中进行一次更改，让网站上的所有页面都反映新的链接文本和新标题。</span><span class="sxs-lookup"><span data-stu-id="247e2-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="247e2-280">检查 Views/_ViewStart.cshtml 文件  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="247e2-281">Views/_ViewStart.cshtml 文件将 Views/Shared/_Layout.cshtml 文件引入到每个视图中   。</span><span class="sxs-lookup"><span data-stu-id="247e2-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="247e2-282">可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。</span><span class="sxs-lookup"><span data-stu-id="247e2-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="247e2-283">更改 Views/HelloWorld/Index.cshtml 视图文件的 `<h2>` 元素  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="247e2-284">稍稍对标题和 `<h2>` 元素进行一些更改，这样可以看出哪一段代码更改了显示。</span><span class="sxs-lookup"><span data-stu-id="247e2-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="247e2-285">上述代码中的 `ViewData["Title"] = "Movie List";` 将 `ViewData` 字典的 `Title` 属性设置为“Movie List”。</span><span class="sxs-lookup"><span data-stu-id="247e2-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="247e2-286">`Title` 属性在布局页面中的 `<title>` HTML 元素中使用：</span><span class="sxs-lookup"><span data-stu-id="247e2-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="247e2-287">保存更改并导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="247e2-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="247e2-288">请注意，浏览器标题、主标题和辅助标题已更改。</span><span class="sxs-lookup"><span data-stu-id="247e2-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="247e2-289">（如果没有在浏览器中看到更改，则可能正在查看缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="247e2-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="247e2-290">在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。）浏览器标题是使用我们在 Index.cshtml 视图模板中设置的 `ViewData["Title"]` 以及在布局文件中添加的额外“ - Movie App”创建的  。</span><span class="sxs-lookup"><span data-stu-id="247e2-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="247e2-291">还要注意，Index.cshtml 视图模板中的内容是如何与 Views/Shared/_Layout.cshtml 视图模板合并的，并且注意单个 HTML 响应被发送到了浏览器   。</span><span class="sxs-lookup"><span data-stu-id="247e2-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="247e2-292">凭借布局模板可以很容易地对应用程序中所有页面进行更改。</span><span class="sxs-lookup"><span data-stu-id="247e2-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="247e2-293">若要了解更多信息，请参阅[布局](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="247e2-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![电影列表视图](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="247e2-295">但我们这一点点“数据”（在此示例中为“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="247e2-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="247e2-296">消息）是硬编码的。</span><span class="sxs-lookup"><span data-stu-id="247e2-296">message) is hard-coded, though.</span></span> <span data-ttu-id="247e2-297">MVC 应用程序有一个“V”（视图），而你已有一个“C”（控制器），但还没有“M”（模型）。</span><span class="sxs-lookup"><span data-stu-id="247e2-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="247e2-298">将数据从控制器传递给视图</span><span class="sxs-lookup"><span data-stu-id="247e2-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="247e2-299">控制器操作会被调用以响应传入的 URL 请求。</span><span class="sxs-lookup"><span data-stu-id="247e2-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="247e2-300">控制器类是编写处理传入浏览器请求的代码的地方。</span><span class="sxs-lookup"><span data-stu-id="247e2-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="247e2-301">控制器从数据源检索数据，并决定将哪些类型的响应发送回浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="247e2-302">可以从控制器使用视图模板来生成并格式化对浏览器的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="247e2-303">控制器负责提供所需的数据，使视图模板能够呈现响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="247e2-304">最佳做法：视图模板不应该直接执行业务逻辑或与数据库进行交互  。</span><span class="sxs-lookup"><span data-stu-id="247e2-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="247e2-305">相反，视图模板应仅使用由控制器提供给它的数据。</span><span class="sxs-lookup"><span data-stu-id="247e2-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="247e2-306">保持此“分离关注点”有助于保持代码干净以及可测试性和可维护性。</span><span class="sxs-lookup"><span data-stu-id="247e2-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="247e2-307">目前，`HelloWorldController` 类中的 `Welcome` 方法采用 `name` 和 `ID` 参数，然后将值直接输出到浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="247e2-308">应将控制器更改为使用视图模板，而不是使控制器将此响应呈现为字符串。</span><span class="sxs-lookup"><span data-stu-id="247e2-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="247e2-309">视图模板会生成动态响应，这意味着必须将适当的数据位从控制器传递给视图以生成响应。</span><span class="sxs-lookup"><span data-stu-id="247e2-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="247e2-310">为此，可以让控制器将视图模板所需的动态数据（参数）放置在视图模板稍后可以访问的 `ViewData` 字典中。</span><span class="sxs-lookup"><span data-stu-id="247e2-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="247e2-311">在 HelloWorldController.cs 中，更改 `Welcome` 方法以将 `Message` 和 `NumTimes` 值添加到 `ViewData` 字典  。</span><span class="sxs-lookup"><span data-stu-id="247e2-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="247e2-312">`ViewData` 字典是一个动态对象，这意味着可以使用任何类型；只有将内容放在其中后 `ViewData` 对象才具有定义的属性。</span><span class="sxs-lookup"><span data-stu-id="247e2-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="247e2-313">[MVC 模型绑定系统](xref:mvc/models/model-binding)自动将命名参数（`name` 和 `numTimes`）从地址栏中的查询字符串映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="247e2-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="247e2-314">完整的 HelloWorldController.cs 文件如下所示  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="247e2-315">`ViewData` 字典对象包含将传递给视图的数据。</span><span class="sxs-lookup"><span data-stu-id="247e2-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="247e2-316">创建一个名为 Views/HelloWorld/Welcome.cshtml 的 Welcome 视图模板  。</span><span class="sxs-lookup"><span data-stu-id="247e2-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="247e2-317">在 Welcome.cshtml 视图模板中创建一个循环，显示“Hello” `NumTimes`  。</span><span class="sxs-lookup"><span data-stu-id="247e2-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="247e2-318">使用以下内容替换 Views/HelloWorld/Welcome.cshtml 的内容  ：</span><span class="sxs-lookup"><span data-stu-id="247e2-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="247e2-319">保存更改并浏览到以下 URL：</span><span class="sxs-lookup"><span data-stu-id="247e2-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="247e2-320">数据取自 URL，并传递给使用 [MVC 模型绑定器](xref:mvc/models/model-binding)的控制器。</span><span class="sxs-lookup"><span data-stu-id="247e2-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="247e2-321">控制器将数据打包到 `ViewData` 字典中，并将该对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="247e2-322">然后，视图将数据作为 HTML 呈现给浏览器。</span><span class="sxs-lookup"><span data-stu-id="247e2-322">The view then renders the data as HTML to the browser.</span></span>

![“隐私”视图，显示了 Welcome 标签以及四个“Hello Rick”短语](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="247e2-324">在上面的示例中，我们使用 `ViewData` 字典将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="247e2-325">稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="247e2-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="247e2-326">传递数据的视图模型方法通常比 `ViewData` 字典方法更为优先。</span><span class="sxs-lookup"><span data-stu-id="247e2-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="247e2-327">有关详细信息，请参阅[何时使用 ViewBag、ViewData 或 TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)。</span><span class="sxs-lookup"><span data-stu-id="247e2-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="247e2-328">在下一个教程中，将创建电影数据库。</span><span class="sxs-lookup"><span data-stu-id="247e2-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="247e2-329">[上一页](adding-controller.md)
> [下一页](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="247e2-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
