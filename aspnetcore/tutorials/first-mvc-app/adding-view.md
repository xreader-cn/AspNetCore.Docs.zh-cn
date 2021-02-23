---
title: 第 3 部分，将视图添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列第 3 部分。
ms.author: riande
ms.date: 01/28/2021
no-loc:
- appsettings.json
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804544"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="196f0-103">第 3 部分，将视图添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="196f0-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="196f0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="196f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="196f0-105">在此部分中，你将修改 `HelloWorldController` 类以使用 [Razor](xref:mvc/views/razor) 视图文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="196f0-106">这顺利封装了为客户端生成 HTML 响应的过程。</span><span class="sxs-lookup"><span data-stu-id="196f0-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="196f0-107">视图模板是使用 Razor 创建的。</span><span class="sxs-lookup"><span data-stu-id="196f0-107">View templates are created using Razor.</span></span> <span data-ttu-id="196f0-108">基于 Razor 的视图模板：</span><span class="sxs-lookup"><span data-stu-id="196f0-108">Razor-based view templates:</span></span>

* <span data-ttu-id="196f0-109">具有 .cshtml 文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="196f0-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="196f0-110">提供一种巧妙的方法来使用 C# 创建 HTML 输出。</span><span class="sxs-lookup"><span data-stu-id="196f0-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="196f0-111">当前，`Index` 方法返回一个字符串，其中包含控制器类中的消息。</span><span class="sxs-lookup"><span data-stu-id="196f0-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="196f0-112">在 `HelloWorldController` 类中，将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="196f0-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="196f0-113">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="196f0-113">The preceding code:</span></span>

* <span data-ttu-id="196f0-114">调用该控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="196f0-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="196f0-115">使用视图模板生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="196f0-116">控制器方法：</span><span class="sxs-lookup"><span data-stu-id="196f0-116">Controller methods:</span></span>

* <span data-ttu-id="196f0-117">称为“操作方法”。</span><span class="sxs-lookup"><span data-stu-id="196f0-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="196f0-118">例如，上述代码中的 `Index` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="196f0-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="196f0-119">通常返回 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 或从 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 派生的类，而不是 `string` 这样的类型。</span><span class="sxs-lookup"><span data-stu-id="196f0-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="196f0-120">添加视图</span><span class="sxs-lookup"><span data-stu-id="196f0-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="196f0-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="196f0-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="196f0-122">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。</span><span class="sxs-lookup"><span data-stu-id="196f0-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="196f0-123">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新项”。</span><span class="sxs-lookup"><span data-stu-id="196f0-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="196f0-124">在“添加新项 - MvcMovie”对话框中：</span><span class="sxs-lookup"><span data-stu-id="196f0-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="196f0-125">在右上角的搜索框中，输入“视图”</span><span class="sxs-lookup"><span data-stu-id="196f0-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="196f0-126">选择“Razor 视图”</span><span class="sxs-lookup"><span data-stu-id="196f0-126">Select **Razor View**</span></span>
* <span data-ttu-id="196f0-127">保持“名称”框的值：Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="196f0-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="196f0-128">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="196f0-128">Select **Add**</span></span>

![“添加新项”对话框](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="196f0-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="196f0-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="196f0-131">为 `HelloWorldController` 添加 `Index` 视图：</span><span class="sxs-lookup"><span data-stu-id="196f0-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="196f0-132">添加一个名为“Views/HelloWorld”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="196f0-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="196f0-133">向 Views/HelloWorld 文件夹添加名为“Index.cshtml”的新文件。 </span><span class="sxs-lookup"><span data-stu-id="196f0-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="196f0-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="196f0-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="196f0-135">按 Control 并单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。</span><span class="sxs-lookup"><span data-stu-id="196f0-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="196f0-136">按 Control 并单击“Views/HelloWorld”文件夹，然后单击“添加”>“新文件”。</span><span class="sxs-lookup"><span data-stu-id="196f0-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="196f0-137">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="196f0-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="196f0-138">在左侧窗格中，选择“ASP.NET Core”。</span><span class="sxs-lookup"><span data-stu-id="196f0-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="196f0-139">选择中心窗格中的 Razor 视图 - 空。</span><span class="sxs-lookup"><span data-stu-id="196f0-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="196f0-140">在“名称”框中键入“Index”。</span><span class="sxs-lookup"><span data-stu-id="196f0-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="196f0-141">选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="196f0-141">Select **New**.</span></span>

  ![“添加新项”对话框](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="196f0-143">使用以下内容替换 Razor 视图文件 Views/HelloWorld/Index.cshtml 的内容：</span><span class="sxs-lookup"><span data-stu-id="196f0-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="196f0-144">导航到 `https://localhost:{PORT}/HelloWorld`：</span><span class="sxs-lookup"><span data-stu-id="196f0-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="196f0-145">`HelloWorldController` 中的 `Index` 方法运行 `return View();` 语句，指定此方法应使用视图模板文件来呈现对浏览器的响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="196f0-146">由于未指定视图模板文件名称，因此 MVC 默认使用默认视图文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="196f0-147">如果未指定视图文件名称，则返回默认视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="196f0-148">默认视图与操作方法的名称相同，在本例中为 `Index`。</span><span class="sxs-lookup"><span data-stu-id="196f0-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="196f0-149">使用视图模板 /Views/HelloWorld/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="196f0-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="196f0-150">下图显示了视图中硬编码的</span><span class="sxs-lookup"><span data-stu-id="196f0-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="196f0-151">字符串“Hello from our View Template!”：</span><span class="sxs-lookup"><span data-stu-id="196f0-151">hard-coded in the view:</span></span>

  ![浏览器窗口](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="196f0-153">更改视图和布局页面</span><span class="sxs-lookup"><span data-stu-id="196f0-153">Change views and layout pages</span></span>

<span data-ttu-id="196f0-154">选择菜单链接“MvcMovie”、“主页”和“隐私”  。</span><span class="sxs-lookup"><span data-stu-id="196f0-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="196f0-155">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="196f0-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="196f0-156">菜单布局是在 Views/Shared/_Layout.cshtml 文件中实现的。</span><span class="sxs-lookup"><span data-stu-id="196f0-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="196f0-157">打开 Views/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="196f0-158">[布局](xref:mvc/views/layout)模板允许：</span><span class="sxs-lookup"><span data-stu-id="196f0-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="196f0-159">在一个位置指定站点的 HTML 容器布局。</span><span class="sxs-lookup"><span data-stu-id="196f0-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="196f0-160">在该站点的多个页面上应用 HTML 容器布局。</span><span class="sxs-lookup"><span data-stu-id="196f0-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="196f0-161">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="196f0-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="196f0-162">`RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中。</span><span class="sxs-lookup"><span data-stu-id="196f0-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="196f0-163">例如，如果选择“隐私”链接，Views/Home/Privacy.cshtml 视图将在 `RenderBody` 方法中呈现 。</span><span class="sxs-lookup"><span data-stu-id="196f0-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="196f0-164">更改布局文件中的标题、页脚和菜单链接</span><span class="sxs-lookup"><span data-stu-id="196f0-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="196f0-165">将 Views/Shared/_Layout.cshtml 文件的内容替换为以下标记。</span><span class="sxs-lookup"><span data-stu-id="196f0-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="196f0-166">突出显示所作更改：</span><span class="sxs-lookup"><span data-stu-id="196f0-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="196f0-167">上述标记进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="196f0-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="196f0-168">`MvcMovie` 更改为 `Movie App` 三次。</span><span class="sxs-lookup"><span data-stu-id="196f0-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="196f0-169">定位点元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 更改为 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="196f0-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="196f0-170">在前面的标记中，省略了 `asp-area=""` [定位点标记帮助程序特性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)和特性值，因为此应用未使用[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="196f0-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="196f0-171">**说明**：`Movies` 控制器尚未实现。</span><span class="sxs-lookup"><span data-stu-id="196f0-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="196f0-172">此时，`Movie App` 链接不起作用。</span><span class="sxs-lookup"><span data-stu-id="196f0-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="196f0-173">保存更改并选择“隐私”链接。</span><span class="sxs-lookup"><span data-stu-id="196f0-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="196f0-174">请注意浏览器选项卡上的标题现在显示的是“隐私策略 - 电影应用”，而不是“隐私策略 - Mvc 电影”：</span><span class="sxs-lookup"><span data-stu-id="196f0-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![“隐私”选项卡](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="196f0-176">选择“主页”链接。</span><span class="sxs-lookup"><span data-stu-id="196f0-176">Select the **Home** link.</span></span>

<span data-ttu-id="196f0-177">请注意，标题和定位点文本显示“电影应用”。</span><span class="sxs-lookup"><span data-stu-id="196f0-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="196f0-178">在布局模板中进行了一次更改，网站上的所有页面都反映新的链接文本和新标题。</span><span class="sxs-lookup"><span data-stu-id="196f0-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="196f0-179">检查 Views/_ViewStart.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="196f0-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="196f0-180">Views/_ViewStart.cshtml 文件将 Views/Shared/_Layout.cshtml 文件引入到每个视图中 。</span><span class="sxs-lookup"><span data-stu-id="196f0-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="196f0-181">可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="196f0-182">打开 Views/HelloWorld/Index.cshtml 视图文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="196f0-183">更改标题和 `<h2>` 元素，如以下突出显示：</span><span class="sxs-lookup"><span data-stu-id="196f0-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="196f0-184">标题和 `<h2>` 元素略有不同，因此可以清楚地看出代码的哪一部分更改了显示。</span><span class="sxs-lookup"><span data-stu-id="196f0-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="196f0-185">上述代码中的 `ViewData["Title"] = "Movie List";` 将 `ViewData` 字典的 `Title` 属性设置为“Movie List”。</span><span class="sxs-lookup"><span data-stu-id="196f0-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="196f0-186">`Title` 属性在布局页面中的 `<title>` HTML 元素中使用：</span><span class="sxs-lookup"><span data-stu-id="196f0-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="196f0-187">保存更改并导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="196f0-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="196f0-188">请注意，以下内容已更改：</span><span class="sxs-lookup"><span data-stu-id="196f0-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="196f0-189">浏览器标题。</span><span class="sxs-lookup"><span data-stu-id="196f0-189">Browser title.</span></span>
* <span data-ttu-id="196f0-190">主标题。</span><span class="sxs-lookup"><span data-stu-id="196f0-190">Primary heading.</span></span>
* <span data-ttu-id="196f0-191">二级标题。</span><span class="sxs-lookup"><span data-stu-id="196f0-191">Secondary headings.</span></span>

<span data-ttu-id="196f0-192">如果浏览器中没有任何更改，则可能是正在查看的缓存内容。</span><span class="sxs-lookup"><span data-stu-id="196f0-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="196f0-193">在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="196f0-194">浏览器标题是使用我们在 Index.cshtml 视图模板中设置的 `ViewData["Title"]` 以及在布局文件中添加的额外“ - Movie App”创建的。</span><span class="sxs-lookup"><span data-stu-id="196f0-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="196f0-195">Index.cshtml 视图模板中的内容与 Views/Shared/_Layout.cshtml 视图模板合并 。</span><span class="sxs-lookup"><span data-stu-id="196f0-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="196f0-196">单个 HTML 响应将发送到浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="196f0-197">凭借布局模板可以轻松地对应用中所有页面进行更改。</span><span class="sxs-lookup"><span data-stu-id="196f0-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="196f0-198">若要了解更多信息，请参阅[布局](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="196f0-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![电影列表视图](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="196f0-200">但是，“数据”的一小部分（即“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="196f0-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="196f0-201">消息）是硬编码的。</span><span class="sxs-lookup"><span data-stu-id="196f0-201">message, is hard-coded however.</span></span> <span data-ttu-id="196f0-202">MVC 应用程序有一个“V”（视图）和一个“C”（控制器），但还没有“M”（模型）。</span><span class="sxs-lookup"><span data-stu-id="196f0-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="196f0-203">将数据从控制器传递给视图</span><span class="sxs-lookup"><span data-stu-id="196f0-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="196f0-204">控制器操作会被调用以响应传入的 URL 请求。</span><span class="sxs-lookup"><span data-stu-id="196f0-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="196f0-205">控制器类是编写处理传入浏览器请求的代码的地方。</span><span class="sxs-lookup"><span data-stu-id="196f0-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="196f0-206">控制器从数据源检索数据，并决定将哪些类型的响应发送回浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="196f0-207">可以从控制器使用视图模板来生成并格式化对浏览器的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="196f0-208">控制器负责提供所需的数据，使视图模板能够呈现响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="196f0-209">视图模板不应：</span><span class="sxs-lookup"><span data-stu-id="196f0-209">View templates should **not**:</span></span>

* <span data-ttu-id="196f0-210">执行业务逻辑</span><span class="sxs-lookup"><span data-stu-id="196f0-210">Do business logic</span></span>
* <span data-ttu-id="196f0-211">直接与数据库交互。</span><span class="sxs-lookup"><span data-stu-id="196f0-211">Interact with a database directly.</span></span>

<span data-ttu-id="196f0-212">视图模板应仅使用由控制器提供给它的数据。</span><span class="sxs-lookup"><span data-stu-id="196f0-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="196f0-213">保持此“分离关注点”有助于保持代码：</span><span class="sxs-lookup"><span data-stu-id="196f0-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="196f0-214">干净。</span><span class="sxs-lookup"><span data-stu-id="196f0-214">Clean.</span></span>
* <span data-ttu-id="196f0-215">可测试。</span><span class="sxs-lookup"><span data-stu-id="196f0-215">Testable.</span></span>
* <span data-ttu-id="196f0-216">可维护。</span><span class="sxs-lookup"><span data-stu-id="196f0-216">Maintainable.</span></span>

<span data-ttu-id="196f0-217">目前，`HelloWorldController` 类中的 `Welcome` 方法采用 `name` 和 `ID` 参数，然后将值直接输出到浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="196f0-218">应将控制器更改为使用视图模板，而不是使控制器将此响应呈现为字符串。</span><span class="sxs-lookup"><span data-stu-id="196f0-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="196f0-219">视图模板会生成动态响应，这意味着必须将适当的数据从控制器传递给视图以生成响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="196f0-220">为此，可以让控制器将视图模板所需的动态数据（参数）放置在 `ViewData` 字典中。</span><span class="sxs-lookup"><span data-stu-id="196f0-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="196f0-221">然后，视图模板可以访问动态数据。</span><span class="sxs-lookup"><span data-stu-id="196f0-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="196f0-222">在 HelloWorldController.cs 中，更改 `Welcome` 方法以将 `Message` 和 `NumTimes` 值添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="196f0-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="196f0-223">`ViewData` 字典是动态对象，这意味着任何类型都可以使用。</span><span class="sxs-lookup"><span data-stu-id="196f0-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="196f0-224">在添加某些内容之前，`ViewData` 对象没有已定义的属性。</span><span class="sxs-lookup"><span data-stu-id="196f0-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="196f0-225">[MVC 模型绑定系统](xref:mvc/models/model-binding)自动将命名参数 `name` 和 `numTimes` 从查询字符串映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="196f0-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="196f0-226">完整的 `HelloWorldController`：</span><span class="sxs-lookup"><span data-stu-id="196f0-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="196f0-227">`ViewData` 字典对象包含将传递给视图的数据。</span><span class="sxs-lookup"><span data-stu-id="196f0-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="196f0-228">创建一个名为 Views/HelloWorld/Welcome.cshtml 的 Welcome 视图模板。</span><span class="sxs-lookup"><span data-stu-id="196f0-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="196f0-229">在 Welcome.cshtml 视图模板中创建一个循环，显示“Hello” `NumTimes`。</span><span class="sxs-lookup"><span data-stu-id="196f0-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="196f0-230">使用以下内容替换 Views/HelloWorld/Welcome.cshtml 的内容：</span><span class="sxs-lookup"><span data-stu-id="196f0-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="196f0-231">保存更改并浏览到以下 URL：</span><span class="sxs-lookup"><span data-stu-id="196f0-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="196f0-232">数据取自 URL，并传递给使用 [MVC 模型绑定器](xref:mvc/models/model-binding)的控制器。</span><span class="sxs-lookup"><span data-stu-id="196f0-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="196f0-233">控制器将数据打包到 `ViewData` 字典中，并将该对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="196f0-234">然后，视图将数据作为 HTML 呈现给浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-234">The view then renders the data as HTML to the browser.</span></span>

![“隐私”视图，显示了 Welcome 标签以及四个“Hello Rick”短语](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="196f0-236">在前面的示例中，我们使用 `ViewData` 字典将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="196f0-237">稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="196f0-238">传递数据的视图模型方法比 `ViewData` 字典方法更为优先。</span><span class="sxs-lookup"><span data-stu-id="196f0-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="196f0-239">在下一个教程中，将创建电影数据库。</span><span class="sxs-lookup"><span data-stu-id="196f0-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="196f0-240">[上一篇：添加控制器](adding-controller.md)
> [下一篇：添加模型](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="196f0-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="196f0-241">在本部分中，将修改 `HelloWorldController` 类，进而使用 [Razor](xref:mvc/views/razor) 视图文件来顺利封装为客户端生成 HTML 响应的过程。</span><span class="sxs-lookup"><span data-stu-id="196f0-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="196f0-242">使用 Razor 创建视图模板文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-242">You create a view template file using Razor.</span></span> <span data-ttu-id="196f0-243">基于 Razor 的视图模板具有 .cshtml 文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="196f0-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="196f0-244">它们提供了一种巧妙的方法来使用 C# 创建 HTML 输出。</span><span class="sxs-lookup"><span data-stu-id="196f0-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="196f0-245">当前，`Index` 方法返回带有在控制器类中硬编码的消息的字符串。</span><span class="sxs-lookup"><span data-stu-id="196f0-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="196f0-246">在 `HelloWorldController` 类中，将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="196f0-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="196f0-247">上面的代码调用控制器的 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 方法。</span><span class="sxs-lookup"><span data-stu-id="196f0-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="196f0-248">它使用视图模板来生成 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="196f0-249">控制器方法（亦称为“操作方法”，如上面的 `Index` 方法）通常返回 <xref:Microsoft.AspNetCore.Mvc.IActionResult>（或派生自 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 的类），而不是 `string` 等类型。</span><span class="sxs-lookup"><span data-stu-id="196f0-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="196f0-250">添加视图</span><span class="sxs-lookup"><span data-stu-id="196f0-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="196f0-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="196f0-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="196f0-252">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。</span><span class="sxs-lookup"><span data-stu-id="196f0-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="196f0-253">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新项”。</span><span class="sxs-lookup"><span data-stu-id="196f0-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="196f0-254">在“添加新项 - MvcMovie”对话框中</span><span class="sxs-lookup"><span data-stu-id="196f0-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="196f0-255">在右上角的搜索框中，输入“视图”</span><span class="sxs-lookup"><span data-stu-id="196f0-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="196f0-256">选择“Razor 视图”</span><span class="sxs-lookup"><span data-stu-id="196f0-256">Select **Razor View**</span></span>

  * <span data-ttu-id="196f0-257">保持“名称”框的值：Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="196f0-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="196f0-258">选择“添加”</span><span class="sxs-lookup"><span data-stu-id="196f0-258">Select **Add**</span></span>

![“添加新项”对话框](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="196f0-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="196f0-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="196f0-261">为 `HelloWorldController` 添加 `Index` 视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="196f0-262">添加一个名为“Views/HelloWorld”的新文件夹。</span><span class="sxs-lookup"><span data-stu-id="196f0-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="196f0-263">向 Views/HelloWorld 文件夹添加名为“Index.cshtml”的新文件。 </span><span class="sxs-lookup"><span data-stu-id="196f0-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="196f0-264">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="196f0-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="196f0-265">右键单击“视图”文件夹，然后单击“添加”>“新文件夹”，并将文件夹命名为“HelloWorld”。</span><span class="sxs-lookup"><span data-stu-id="196f0-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="196f0-266">右键单击“Views/HelloWorld”文件夹，然后单击“添加”>“新文件”。</span><span class="sxs-lookup"><span data-stu-id="196f0-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="196f0-267">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="196f0-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="196f0-268">在左窗格中，选择“Web”。</span><span class="sxs-lookup"><span data-stu-id="196f0-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="196f0-269">在中间窗格中，选择“空 HTML 文件”。</span><span class="sxs-lookup"><span data-stu-id="196f0-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="196f0-270">在“名称”框中键入“Index.cshtml”。</span><span class="sxs-lookup"><span data-stu-id="196f0-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="196f0-271">选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="196f0-271">Select **New**.</span></span>

![“添加新项”对话框](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="196f0-273">使用以下内容替换 Razor 视图文件 Views/HelloWorld/Index.cshtml 的内容：</span><span class="sxs-lookup"><span data-stu-id="196f0-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="196f0-274">导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="196f0-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="196f0-275">`HelloWorldController` 中的 `Index` 方法作用不大；它运行 `return View();` 语句，指定此方法应使用视图模板文件来呈现对浏览器的响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="196f0-276">由于未指定视图模板文件名称，MVC 默认使用默认视图文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="196f0-277">默认视图文件具有与方法 (`Index`) 相同的名称，因此在 /Views/HelloWorld/Index.cshtml 中使用。</span><span class="sxs-lookup"><span data-stu-id="196f0-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="196f0-278">下面图片显示了视图中硬编码的</span><span class="sxs-lookup"><span data-stu-id="196f0-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="196f0-279">字符串“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="196f0-279">hard-coded in the view.</span></span>

![浏览器窗口](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="196f0-281">更改视图和布局页面</span><span class="sxs-lookup"><span data-stu-id="196f0-281">Change views and layout pages</span></span>

<span data-ttu-id="196f0-282">选择菜单链接（“MvcMovie”、“首页”和“隐私”）  。</span><span class="sxs-lookup"><span data-stu-id="196f0-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="196f0-283">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="196f0-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="196f0-284">菜单布局是在 Views/Shared/_Layout.cshtml 文件中实现的。</span><span class="sxs-lookup"><span data-stu-id="196f0-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="196f0-285">打开 Views/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="196f0-286">[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="196f0-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="196f0-287">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="196f0-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="196f0-288">`RenderBody` 是显示创建的所有特定于视图的页面的占位符，已包装在布局页面中。</span><span class="sxs-lookup"><span data-stu-id="196f0-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="196f0-289">例如，如果选择“隐私”链接，Views/Home/Privacy.cshtml 视图将在 `RenderBody` 方法中呈现 。</span><span class="sxs-lookup"><span data-stu-id="196f0-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="196f0-290">更改布局文件中的标题、页脚和菜单链接</span><span class="sxs-lookup"><span data-stu-id="196f0-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="196f0-291">在标题和页脚元素中，将 `MvcMovie` 更改为 `Movie App`。</span><span class="sxs-lookup"><span data-stu-id="196f0-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="196f0-292">将定位点元素 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` 更改为 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`。</span><span class="sxs-lookup"><span data-stu-id="196f0-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="196f0-293">下列标记显示了突出显示的更改：</span><span class="sxs-lookup"><span data-stu-id="196f0-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="196f0-294">在前面的标记中，省略了 `asp-area` [定位点标记帮助程序属性](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)，因为此应用未使用[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="196f0-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="196f0-295">**说明**：`Movies` 控制器尚未实现。</span><span class="sxs-lookup"><span data-stu-id="196f0-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="196f0-296">此时，`Movie App` 链接不起作用。</span><span class="sxs-lookup"><span data-stu-id="196f0-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="196f0-297">保存更改并选择“隐私”链接。</span><span class="sxs-lookup"><span data-stu-id="196f0-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="196f0-298">请注意浏览器选项卡上的标题现在显示的是“隐私策略 - 电影应用”，而不是“隐私策略 - Mvc 电影”：</span><span class="sxs-lookup"><span data-stu-id="196f0-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![“隐私”选项卡](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="196f0-300">选择“主页”链接，请注意，标题和定位文本还会显示“电影应用” 。</span><span class="sxs-lookup"><span data-stu-id="196f0-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="196f0-301">我们能够在布局模板中进行一次更改，让网站上的所有页面都反映新的链接文本和新标题。</span><span class="sxs-lookup"><span data-stu-id="196f0-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="196f0-302">检查 Views/_ViewStart.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="196f0-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="196f0-303">Views/_ViewStart.cshtml 文件将 Views/Shared/_Layout.cshtml 文件引入到每个视图中 。</span><span class="sxs-lookup"><span data-stu-id="196f0-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="196f0-304">可以使用 `Layout` 属性设置不同的布局视图，或将它设置为 `null`，这样将不会使用任何布局文件。</span><span class="sxs-lookup"><span data-stu-id="196f0-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="196f0-305">更改 Views/HelloWorld/Index.cshtml 视图文件的 `<h2>` 元素：</span><span class="sxs-lookup"><span data-stu-id="196f0-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="196f0-306">稍稍对标题和 `<h2>` 元素进行一些更改，这样可以看出哪一段代码更改了显示。</span><span class="sxs-lookup"><span data-stu-id="196f0-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="196f0-307">上述代码中的 `ViewData["Title"] = "Movie List";` 将 `ViewData` 字典的 `Title` 属性设置为“Movie List”。</span><span class="sxs-lookup"><span data-stu-id="196f0-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="196f0-308">`Title` 属性在布局页面中的 `<title>` HTML 元素中使用：</span><span class="sxs-lookup"><span data-stu-id="196f0-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="196f0-309">保存更改并导航到 `https://localhost:{PORT}/HelloWorld`。</span><span class="sxs-lookup"><span data-stu-id="196f0-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="196f0-310">请注意，浏览器标题、主标题和辅助标题已更改。</span><span class="sxs-lookup"><span data-stu-id="196f0-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="196f0-311">（如果没有在浏览器中看到更改，则可能正在查看缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="196f0-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="196f0-312">在浏览器中按 Ctrl + F5 强制加载来自服务器的响应。）浏览器标题是使用我们在 Index.cshtml 视图模板中设置的 `ViewData["Title"]` 以及在布局文件中添加的额外“ - Movie App”创建的。</span><span class="sxs-lookup"><span data-stu-id="196f0-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="196f0-313">还要注意，Index.cshtml 视图模板中的内容是如何与 Views/Shared/_Layout.cshtml 视图模板合并的，并且注意单个 HTML 响应被发送到了浏览器 。</span><span class="sxs-lookup"><span data-stu-id="196f0-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="196f0-314">凭借布局模板可以很容易地对应用程序中所有页面进行更改。</span><span class="sxs-lookup"><span data-stu-id="196f0-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="196f0-315">若要了解更多信息，请参阅[布局](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="196f0-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![电影列表视图](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="196f0-317">但我们这一点点“数据”（在此示例中为“Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="196f0-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="196f0-318">消息）是硬编码的。</span><span class="sxs-lookup"><span data-stu-id="196f0-318">message) is hard-coded, though.</span></span> <span data-ttu-id="196f0-319">MVC 应用程序有一个“V”（视图），而你已有一个“C”（控制器），但还没有“M”（模型）。</span><span class="sxs-lookup"><span data-stu-id="196f0-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="196f0-320">将数据从控制器传递给视图</span><span class="sxs-lookup"><span data-stu-id="196f0-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="196f0-321">控制器操作会被调用以响应传入的 URL 请求。</span><span class="sxs-lookup"><span data-stu-id="196f0-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="196f0-322">控制器类是编写处理传入浏览器请求的代码的地方。</span><span class="sxs-lookup"><span data-stu-id="196f0-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="196f0-323">控制器从数据源检索数据，并决定将哪些类型的响应发送回浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="196f0-324">可以从控制器使用视图模板来生成并格式化对浏览器的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="196f0-325">控制器负责提供所需的数据，使视图模板能够呈现响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="196f0-326">最佳做法：视图模板不应该直接执行业务逻辑或与数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="196f0-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="196f0-327">相反，视图模板应仅使用由控制器提供给它的数据。</span><span class="sxs-lookup"><span data-stu-id="196f0-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="196f0-328">保持此“分离关注点”有助于保持代码干净以及可测试性和可维护性。</span><span class="sxs-lookup"><span data-stu-id="196f0-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="196f0-329">目前，`HelloWorldController` 类中的 `Welcome` 方法采用 `name` 和 `ID` 参数，然后将值直接输出到浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="196f0-330">应将控制器更改为使用视图模板，而不是使控制器将此响应呈现为字符串。</span><span class="sxs-lookup"><span data-stu-id="196f0-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="196f0-331">视图模板会生成动态响应，这意味着必须将适当的数据位从控制器传递给视图以生成响应。</span><span class="sxs-lookup"><span data-stu-id="196f0-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="196f0-332">为此，可以让控制器将视图模板所需的动态数据（参数）放置在视图模板稍后可以访问的 `ViewData` 字典中。</span><span class="sxs-lookup"><span data-stu-id="196f0-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="196f0-333">在 HelloWorldController.cs 中，更改 `Welcome` 方法以将 `Message` 和 `NumTimes` 值添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="196f0-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="196f0-334">`ViewData` 字典是一个动态对象，这意味着可以使用任何类型；只有将内容放在其中后 `ViewData` 对象才具有定义的属性。</span><span class="sxs-lookup"><span data-stu-id="196f0-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="196f0-335">[MVC 模型绑定系统](xref:mvc/models/model-binding)自动将命名参数（`name` 和 `numTimes`）从地址栏中的查询字符串映射到方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="196f0-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="196f0-336">完整的 HelloWorldController.cs 文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="196f0-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="196f0-337">`ViewData` 字典对象包含将传递给视图的数据。</span><span class="sxs-lookup"><span data-stu-id="196f0-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="196f0-338">创建一个名为 Views/HelloWorld/Welcome.cshtml 的 Welcome 视图模板。</span><span class="sxs-lookup"><span data-stu-id="196f0-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="196f0-339">在 Welcome.cshtml 视图模板中创建一个循环，显示“Hello” `NumTimes`。</span><span class="sxs-lookup"><span data-stu-id="196f0-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="196f0-340">使用以下内容替换 Views/HelloWorld/Welcome.cshtml 的内容：</span><span class="sxs-lookup"><span data-stu-id="196f0-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="196f0-341">保存更改并浏览到以下 URL：</span><span class="sxs-lookup"><span data-stu-id="196f0-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="196f0-342">数据取自 URL，并传递给使用 [MVC 模型绑定器](xref:mvc/models/model-binding)的控制器。</span><span class="sxs-lookup"><span data-stu-id="196f0-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="196f0-343">控制器将数据打包到 `ViewData` 字典中，并将该对象传递给视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="196f0-344">然后，视图将数据作为 HTML 呈现给浏览器。</span><span class="sxs-lookup"><span data-stu-id="196f0-344">The view then renders the data as HTML to the browser.</span></span>

![“隐私”视图，显示了 Welcome 标签以及四个“Hello Rick”短语](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="196f0-346">在上面的示例中，我们使用 `ViewData` 字典将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="196f0-347">稍后在本教程中，我们将使用视图模型将数据从控制器传递给视图。</span><span class="sxs-lookup"><span data-stu-id="196f0-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="196f0-348">传递数据的视图模型方法通常比 `ViewData` 字典方法更为优先。</span><span class="sxs-lookup"><span data-stu-id="196f0-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="196f0-349">有关详细信息，请参阅[何时使用 ViewBag、ViewData 或 TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)。</span><span class="sxs-lookup"><span data-stu-id="196f0-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="196f0-350">在下一个教程中，将创建电影数据库。</span><span class="sxs-lookup"><span data-stu-id="196f0-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="196f0-351">[上一页](adding-controller.md)
> [下一页](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="196f0-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
