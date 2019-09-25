---
title: ASP.NET Core 中的 Razor 页面介绍
author: Rick-Anderson
description: 了解 ASP.NET Core 中的 Razor 页面如何使基于页面的编码方式比使用 MVC 更简单高效。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 09/19/2019
uid: razor-pages/index
ms.openlocfilehash: 7e27bbc84816bd282b2b8de936772eb5c597d74f
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207371"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="07887-103">ASP.NET Core 中的 Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="07887-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07887-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="07887-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="07887-105">通过 Razor Pages 对基于页面的场景编码比使用控制器和视图更轻松、更高效。</span><span class="sxs-lookup"><span data-stu-id="07887-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="07887-106">若要查找使用模型视图控制器方法的教程，请参阅 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="07887-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="07887-107">本文档介绍 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="07887-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="07887-108">它并不是分步教程。</span><span class="sxs-lookup"><span data-stu-id="07887-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="07887-109">如果认为某些部分过于复杂，请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="07887-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="07887-110">有关 ASP.NET Core 的概述，请参阅 [ASP.NET Core 简介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="07887-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07887-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="07887-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07887-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07887-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07887-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07887-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07887-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="07887-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="07887-115">创建 Razor Pages 项目</span><span class="sxs-lookup"><span data-stu-id="07887-115">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07887-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07887-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07887-117">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何创建 Razor Pages 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="07887-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07887-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07887-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07887-119">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="07887-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07887-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="07887-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="07887-121">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="07887-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="07887-122">在 Visual Studio for Mac 中打开生成的 .csproj  文件。</span><span class="sxs-lookup"><span data-stu-id="07887-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="07887-123">Razor 页面</span><span class="sxs-lookup"><span data-stu-id="07887-123">Razor Pages</span></span>

<span data-ttu-id="07887-124">Startup.cs 中已启用 Razor 页面  ：</span><span class="sxs-lookup"><span data-stu-id="07887-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12)]

<span data-ttu-id="07887-125">请考虑一个基本页面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="07887-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="07887-126">前面的代码与具有控制器和视图的 ASP.NET Core 应用中使用的 [Razor 视图文件](xref:tutorials/first-mvc-app/adding-view)非常相似。</span><span class="sxs-lookup"><span data-stu-id="07887-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="07887-127">不同之处在于 [@page](xref:mvc/views/razor#page) 指令。</span><span class="sxs-lookup"><span data-stu-id="07887-127">What makes it different is the [@page](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="07887-128">`@page` 使文件转换为一个 MVC 操作 ，这意味着它将直接处理请求，而无需通过控制器处理。</span><span class="sxs-lookup"><span data-stu-id="07887-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="07887-129">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="07887-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="07887-130">`@page` 会影响其他 [Razor](xref:mvc/views/razor) 构造的行为。</span><span class="sxs-lookup"><span data-stu-id="07887-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="07887-131">Razor Pages 文件名有 *.cshtml* 后缀。</span><span class="sxs-lookup"><span data-stu-id="07887-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="07887-132">将在以下两个文件中显示使用 `PageModel` 类的类似页面。</span><span class="sxs-lookup"><span data-stu-id="07887-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="07887-133">Pages/Index2.cshtml  文件：</span><span class="sxs-lookup"><span data-stu-id="07887-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="07887-134">Pages/Index2.cshtml.cs 页面模型  ：</span><span class="sxs-lookup"><span data-stu-id="07887-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="07887-135">按照惯例，`PageModel` 类文件的名称与追加 .cs  的 Razor 页面文件名称相同。</span><span class="sxs-lookup"><span data-stu-id="07887-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="07887-136">例如，前面的 Razor 页面的名称为 Pages/Index2.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="07887-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="07887-137">包含 `PageModel` 类的文件的名称为 Pages/Index2.cshtml.cs  。</span><span class="sxs-lookup"><span data-stu-id="07887-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="07887-138">页面的 URL 路径的关联由页面在文件系统中的位置决定。</span><span class="sxs-lookup"><span data-stu-id="07887-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="07887-139">下表显示了 Razor 页面路径及匹配的 URL：</span><span class="sxs-lookup"><span data-stu-id="07887-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="07887-140">文件名和路径</span><span class="sxs-lookup"><span data-stu-id="07887-140">File name and path</span></span>               | <span data-ttu-id="07887-141">匹配的 URL</span><span class="sxs-lookup"><span data-stu-id="07887-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="07887-142">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="07887-143">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="07887-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="07887-144">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="07887-145">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="07887-146">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="07887-147">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="07887-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="07887-148">注意：</span><span class="sxs-lookup"><span data-stu-id="07887-148">Notes:</span></span>

* <span data-ttu-id="07887-149">默认情况下，运行时在“Pages”文件夹中查找 Razor 页面文件。 </span><span class="sxs-lookup"><span data-stu-id="07887-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="07887-150">URL 未包含页面时，`Index` 为默认页面。</span><span class="sxs-lookup"><span data-stu-id="07887-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="07887-151">编写基本窗体</span><span class="sxs-lookup"><span data-stu-id="07887-151">Write a basic form</span></span>

<span data-ttu-id="07887-152">由于 Razor 页面的设计，在构建应用时可轻松实施用于 Web 浏览器的常用模式。</span><span class="sxs-lookup"><span data-stu-id="07887-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="07887-153">[模型绑定](xref:mvc/models/model-binding)、[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 HTML 帮助程序均只可用于 Razor 页面类中定义的属性。 </span><span class="sxs-lookup"><span data-stu-id="07887-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="07887-154">请参考为 `Contact` 模型实现基本的“联系我们”窗体的页面：</span><span class="sxs-lookup"><span data-stu-id="07887-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="07887-155">在本文档中的示例中，`DbContext` 在 [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) 文件中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="07887-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="07887-156">数据模型：</span><span class="sxs-lookup"><span data-stu-id="07887-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="07887-157">数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="07887-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="07887-158">Pages/Create.cshtml  视图文件：</span><span class="sxs-lookup"><span data-stu-id="07887-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="07887-159">Pages/Create.cshtml.cs 页面模型  ：</span><span class="sxs-lookup"><span data-stu-id="07887-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="07887-160">按照惯例，`PageModel` 类命名为 `<PageName>Model`并且它与页面位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="07887-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="07887-161">使用 `PageModel` 类，可以将页面的逻辑与其展示分离开来。</span><span class="sxs-lookup"><span data-stu-id="07887-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="07887-162">它定义了页面处理程序，用于处理发送到页面的请求和用于呈现页面的数据。</span><span class="sxs-lookup"><span data-stu-id="07887-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="07887-163">这种隔离可实现：</span><span class="sxs-lookup"><span data-stu-id="07887-163">This separation allows:</span></span>

* <span data-ttu-id="07887-164">通过[依赖关系注入](xref:fundamentals/dependency-injection)管理页面依赖项。</span><span class="sxs-lookup"><span data-stu-id="07887-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="07887-165">单元测试</span><span class="sxs-lookup"><span data-stu-id="07887-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="07887-166">页面包含 `OnPostAsync` 处理程序方法  ，它在 `POST` 请求上运行（当用户发布窗体时）。</span><span class="sxs-lookup"><span data-stu-id="07887-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="07887-167">可以添加任何 HTTP 谓词的处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="07887-168">最常见的处理程序是：</span><span class="sxs-lookup"><span data-stu-id="07887-168">The most common handlers are:</span></span>

* <span data-ttu-id="07887-169">`OnGet`，用于初始化页面所需的状态。</span><span class="sxs-lookup"><span data-stu-id="07887-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="07887-170">在上面的代码中，`OnGet` 方法显示 CreateModel.cshtml Razor Page  。</span><span class="sxs-lookup"><span data-stu-id="07887-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="07887-171">`OnPost`，用于处理窗体提交。</span><span class="sxs-lookup"><span data-stu-id="07887-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="07887-172">`Async` 命名后缀为可选，但是按照惯例通常会将它用于异步函数。</span><span class="sxs-lookup"><span data-stu-id="07887-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="07887-173">前面的代码通常用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="07887-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="07887-174">如果你熟悉使用控制器和视图的 ASP.NET 应用：</span><span class="sxs-lookup"><span data-stu-id="07887-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="07887-175">前面示例中的 `OnPostAsync` 代码类似于典型的控制器代码。</span><span class="sxs-lookup"><span data-stu-id="07887-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="07887-176">大多数 MVC 基元（例如[模型绑定](xref:mvc/models/model-binding)、[验证](xref:mvc/models/validation)和操作结果）通过控制器和通过 Razor Pages 的运作方式相同。</span><span class="sxs-lookup"><span data-stu-id="07887-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="07887-177">之前的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="07887-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="07887-178">`OnPostAsync` 的基本流：</span><span class="sxs-lookup"><span data-stu-id="07887-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="07887-179">检查验证错误。</span><span class="sxs-lookup"><span data-stu-id="07887-179">Check for validation errors.</span></span>

* <span data-ttu-id="07887-180">如果没有错误，则保存数据并重定向。</span><span class="sxs-lookup"><span data-stu-id="07887-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="07887-181">如果有错误，则再次显示页面并附带验证消息。</span><span class="sxs-lookup"><span data-stu-id="07887-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="07887-182">很多情况下，都会在客户端上检测到验证错误，并且从不将它们提交到服务器。</span><span class="sxs-lookup"><span data-stu-id="07887-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="07887-183">Pages/Create.cshtml  视图文件：</span><span class="sxs-lookup"><span data-stu-id="07887-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="07887-184">Pages/Create.cshtml 中呈现的 HTML  ：</span><span class="sxs-lookup"><span data-stu-id="07887-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="07887-185">在前面的代码中，发布窗体：</span><span class="sxs-lookup"><span data-stu-id="07887-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="07887-186">对于有效数据：</span><span class="sxs-lookup"><span data-stu-id="07887-186">With valid data:</span></span>

  * <span data-ttu-id="07887-187">`OnPostAsync` 处理程序方法调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="07887-188">`RedirectToPage` 返回 <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> 的实例。</span><span class="sxs-lookup"><span data-stu-id="07887-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="07887-189">`RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="07887-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="07887-190">是操作结果。</span><span class="sxs-lookup"><span data-stu-id="07887-190">Is an action result.</span></span>
    * <span data-ttu-id="07887-191">类似于 `RedirectToAction` 或 `RedirectToRoute`（用于控制器和视图）。</span><span class="sxs-lookup"><span data-stu-id="07887-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="07887-192">针对页面自定义。</span><span class="sxs-lookup"><span data-stu-id="07887-192">Is customized for pages.</span></span> <span data-ttu-id="07887-193">在前面的示例中，它将重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="07887-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="07887-194">[页面 URL 生成](#url_gen)部分中详细介绍了 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="07887-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="07887-195">对于传递给服务器的验证错误：</span><span class="sxs-lookup"><span data-stu-id="07887-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="07887-196">`OnPostAsync` 处理程序方法调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="07887-197">`Page` 返回 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 的实例。</span><span class="sxs-lookup"><span data-stu-id="07887-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="07887-198">返回 `Page` 的过程与控制器中的操作返回 `View` 的过程相似。</span><span class="sxs-lookup"><span data-stu-id="07887-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="07887-199">`PageResult` 是处理程序方法的默认返回类型。</span><span class="sxs-lookup"><span data-stu-id="07887-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="07887-200">返回 `void` 的处理程序方法将显示页面。</span><span class="sxs-lookup"><span data-stu-id="07887-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="07887-201">在前面的示例中，在 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 中的值结果不返回 false 的情况下发布窗体。</span><span class="sxs-lookup"><span data-stu-id="07887-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="07887-202">在此示例中，客户端上不显示任何验证错误。</span><span class="sxs-lookup"><span data-stu-id="07887-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="07887-203">本文档的后面将介绍验证错误处理。</span><span class="sxs-lookup"><span data-stu-id="07887-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="07887-204">对于客户端验证检测到的验证错误：</span><span class="sxs-lookup"><span data-stu-id="07887-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="07887-205">数据不  会发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="07887-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="07887-206">本文档的后面将介绍客户端验证。</span><span class="sxs-lookup"><span data-stu-id="07887-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="07887-207">`Customer` 属性使用 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 特性来选择加入模型绑定：</span><span class="sxs-lookup"><span data-stu-id="07887-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="07887-208">`[BindProperty]` 不应  用于包含不应由客户端更改的属性的模型。</span><span class="sxs-lookup"><span data-stu-id="07887-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="07887-209">有关详细信息，请参阅[过度发布](xref:data/ef-rp/crud#overposting)</span><span class="sxs-lookup"><span data-stu-id="07887-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting)</span></span>

<span data-ttu-id="07887-210">默认情况下，Razor 页面只绑定带有非 `GET` 谓词的属性。</span><span class="sxs-lookup"><span data-stu-id="07887-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="07887-211">如果绑定到属性，则无需通过编写代码将 HTTP 数据转换为模型类型。</span><span class="sxs-lookup"><span data-stu-id="07887-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="07887-212">绑定通过使用相同的属性显示窗体字段 (`<input asp-for="Customer.Name">`) 来减少代码，并接受输入。</span><span class="sxs-lookup"><span data-stu-id="07887-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="07887-213">查看 Pages/Create.cshtml 视图文件： </span><span class="sxs-lookup"><span data-stu-id="07887-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="07887-214">在前面的代码中，[输入标记帮助程序](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />`将 HTML `<input>` 元素绑定到 `Customer.Name` 模型表达式。</span><span class="sxs-lookup"><span data-stu-id="07887-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="07887-215">使用 [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) 提供标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="07887-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="07887-216">主页</span><span class="sxs-lookup"><span data-stu-id="07887-216">The home page</span></span>

<span data-ttu-id="07887-217">Index.cshtml 是主页  ：</span><span class="sxs-lookup"><span data-stu-id="07887-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="07887-218">关联的 `PageModel` 类 (Index.cshtml.cs)  ：</span><span class="sxs-lookup"><span data-stu-id="07887-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="07887-219">Index.cshtml 文件包含以下标记  ：</span><span class="sxs-lookup"><span data-stu-id="07887-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="07887-220">`<a /a>`[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 使用 `asp-route-{value}` 属性生成“编辑”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="07887-220">The `<a /a>`[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="07887-221">此链接包含路由数据及联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="07887-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="07887-222">例如 `https://localhost:5001/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="07887-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="07887-223">[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="07887-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="07887-224">Index.cshtml 文件包含用于为每个客户联系人创建删除按钮的标记： </span><span class="sxs-lookup"><span data-stu-id="07887-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="07887-225">呈现的 HTML：</span><span class="sxs-lookup"><span data-stu-id="07887-225">The rendered HTML:</span></span>

```HTML
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="07887-226">删除按钮采用 HTML 呈现，其 [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) 包括参数：</span><span class="sxs-lookup"><span data-stu-id="07887-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="07887-227">`asp-route-id` 属性指定的客户联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="07887-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="07887-228">`asp-page-handler` 属性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="07887-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="07887-229">选中按钮时，向服务器发送窗体 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="07887-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="07887-230">按照惯例，根据方案 `OnPost[handler]Async` 基于 `handler` 参数的值来选择处理程序方法的名称。</span><span class="sxs-lookup"><span data-stu-id="07887-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="07887-231">因为本示例中 `handler` 是 `delete`，因此 `OnPostDeleteAsync` 处理程序方法用于处理 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="07887-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="07887-232">如果 `asp-page-handler` 设置为其他值（如 `remove`），则选择名称为 `OnPostRemoveAsync` 的处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="07887-233">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="07887-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="07887-234">获取来自查询字符串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="07887-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="07887-235">使用 `FindAsync` 查询客户联系人的数据库。</span><span class="sxs-lookup"><span data-stu-id="07887-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="07887-236">如果找到客户联系人，则会将其删除，并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="07887-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="07887-237">调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>，重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="07887-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="07887-238">Edit.cshtml 文件</span><span class="sxs-lookup"><span data-stu-id="07887-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="07887-239">第一行包含 `@page "{id:int}"` 指令。</span><span class="sxs-lookup"><span data-stu-id="07887-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="07887-240">路由约束 `"{id:int}"` 告诉页面接受包含 `int` 路由数据的页面请求。</span><span class="sxs-lookup"><span data-stu-id="07887-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="07887-241">如果页面请求未包含可转换为 `int` 的路由数据，则运行时返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="07887-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="07887-242">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="07887-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="07887-243">Edit.cshtml.cs 文件  ：</span><span class="sxs-lookup"><span data-stu-id="07887-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="07887-244">验证</span><span class="sxs-lookup"><span data-stu-id="07887-244">Validation</span></span>

<span data-ttu-id="07887-245">验证规则：</span><span class="sxs-lookup"><span data-stu-id="07887-245">Validation rules:</span></span>

* <span data-ttu-id="07887-246">在模型类中以声明方式指定。</span><span class="sxs-lookup"><span data-stu-id="07887-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="07887-247">在应用中的所有位置强制执行。</span><span class="sxs-lookup"><span data-stu-id="07887-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="07887-248"><xref:System.ComponentModel.DataAnnotations> 命名空间提供一组内置验证特性，可通过声明方式应用于类或属性。</span><span class="sxs-lookup"><span data-stu-id="07887-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="07887-249">DataAnnotations 还包含 [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 等格式特性，有助于格式设置但不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="07887-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="07887-250">请考虑 `Customer` 模型：</span><span class="sxs-lookup"><span data-stu-id="07887-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="07887-251">使用以下 Create.cshtml 视图文件  ：</span><span class="sxs-lookup"><span data-stu-id="07887-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="07887-252">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="07887-252">The preceding code:</span></span>

* <span data-ttu-id="07887-253">包括 jQuery 和 jQuery 验证脚本。</span><span class="sxs-lookup"><span data-stu-id="07887-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="07887-254">使用 `<div />` 和 `<span />` [标记帮助程序](xref:mvc/views/tag-helpers/intro)以实现：</span><span class="sxs-lookup"><span data-stu-id="07887-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="07887-255">客户端验证。</span><span class="sxs-lookup"><span data-stu-id="07887-255">Client-side validation.</span></span>
  * <span data-ttu-id="07887-256">验证错误呈现。</span><span class="sxs-lookup"><span data-stu-id="07887-256">Validation error rendering.</span></span>

* <span data-ttu-id="07887-257">会生成以下 HTML：[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]</span><span class="sxs-lookup"><span data-stu-id="07887-257">Generates the following HTML: [!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]</span></span>

<span data-ttu-id="07887-258">如果在不使用名称值的情况下发布“创建”窗体，则将显示错误消息“名称字段是必需的”。</span><span class="sxs-lookup"><span data-stu-id="07887-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="07887-259">窗体上。</span><span class="sxs-lookup"><span data-stu-id="07887-259">on the form.</span></span> <span data-ttu-id="07887-260">如果客户端上已启用 JavaScript，浏览器会显示错误，而不会发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="07887-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="07887-261">`[StringLength(10)]` 特性在呈现的 HTML 上生成 `data-val-length-max="10"`。</span><span class="sxs-lookup"><span data-stu-id="07887-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="07887-262">`data-val-length-max` 阻止浏览器输入超过指定最大长度的内容。</span><span class="sxs-lookup"><span data-stu-id="07887-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="07887-263">如果使用 [Fiddler](https://www.telerik.com/fiddler) 等工具来编辑和重播文章：</span><span class="sxs-lookup"><span data-stu-id="07887-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="07887-264">对于长度超过 10 的名称。</span><span class="sxs-lookup"><span data-stu-id="07887-264">With the name longer than 10.</span></span>
* <span data-ttu-id="07887-265">错误消息“字段名称必须是最大长度为 10 的字符串。”</span><span class="sxs-lookup"><span data-stu-id="07887-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="07887-266">返回。</span><span class="sxs-lookup"><span data-stu-id="07887-266">is returned.</span></span>

<span data-ttu-id="07887-267">考虑下列 `Movie` 模型：</span><span class="sxs-lookup"><span data-stu-id="07887-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="07887-268">验证特性指定要对应用这些特性的模型属性强制执行的行为：</span><span class="sxs-lookup"><span data-stu-id="07887-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="07887-269">`Required` 和 `MinimumLength` 特性表示属性必须有值；但用户可输入空格来满足此验证。</span><span class="sxs-lookup"><span data-stu-id="07887-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="07887-270">`RegularExpression` 特性用于限制可输入的字符。</span><span class="sxs-lookup"><span data-stu-id="07887-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="07887-271">在上述代码中，即“Genre”（分类）：</span><span class="sxs-lookup"><span data-stu-id="07887-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="07887-272">只能使用字母。</span><span class="sxs-lookup"><span data-stu-id="07887-272">Must only use letters.</span></span>
  * <span data-ttu-id="07887-273">第一个字母必须为大写。</span><span class="sxs-lookup"><span data-stu-id="07887-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="07887-274">不允许使用空格、数字和特殊字符。</span><span class="sxs-lookup"><span data-stu-id="07887-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="07887-275">`RegularExpression`“Rating”（分级）：</span><span class="sxs-lookup"><span data-stu-id="07887-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="07887-276">要求第一个字符为大写字母。</span><span class="sxs-lookup"><span data-stu-id="07887-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="07887-277">允许在后续空格中使用特殊字符和数字。</span><span class="sxs-lookup"><span data-stu-id="07887-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="07887-278">“PG-13”对“分级”有效，但对于“分类”无效。</span><span class="sxs-lookup"><span data-stu-id="07887-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="07887-279">`Range` 特性将值限制在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="07887-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="07887-280">`StringLength` 特性可以设置字符串属性的最大长度，以及可选的最小长度。</span><span class="sxs-lookup"><span data-stu-id="07887-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="07887-281">从本质上来说，需要值类型（如 `decimal`、`int`、`float`、`DateTime`），但不需要 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="07887-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="07887-282">`Movie` 模型的“创建”页面显示无效值错误：</span><span class="sxs-lookup"><span data-stu-id="07887-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![带有多个 jQuery 客户端验证错误的电影视图表单](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="07887-284">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="07887-284">For more information, see:</span></span>

* [<span data-ttu-id="07887-285">将验证添加到电影应用</span><span class="sxs-lookup"><span data-stu-id="07887-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="07887-286">[ASP.NET Core 中的模型验证](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="07887-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="07887-287">使用 OnGet 处理程序回退来处理 HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="07887-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="07887-288">`HEAD` 请求可检索特定资源的标头。</span><span class="sxs-lookup"><span data-stu-id="07887-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="07887-289">与 `GET` 请求不同，`HEAD` 请求不返回响应正文。</span><span class="sxs-lookup"><span data-stu-id="07887-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="07887-290">通常，针对 `HEAD` 请求创建和调用 `OnHead` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="07887-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="07887-291">如果未定义 `OnHead` 处理程序，则 Razor Pages 会回退到调用 `OnGet` 处理程序。</span><span class="sxs-lookup"><span data-stu-id="07887-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="07887-292">XSRF/CSRF 和 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="07887-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="07887-293">Razor Pages 由 [防伪造验证](xref:security/anti-request-forgery)保护。</span><span class="sxs-lookup"><span data-stu-id="07887-293">Razor Pages are protected by[Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="07887-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 将防伪造令牌注入 HTML 窗体元素。</span><span class="sxs-lookup"><span data-stu-id="07887-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="07887-295">将布局、分区、模板和标记帮助程序用于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="07887-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="07887-296">页面可使用 Razor 视图引擎的所有功能。</span><span class="sxs-lookup"><span data-stu-id="07887-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="07887-297">布局、分区、模板、标记帮助程序、_ViewStart.cshtml  和 _ViewImports.cshtml  的工作方式与它们在传统的 Razor 视图中的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="07887-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="07887-298">让我们使用其中的一些功能来整理此页面。</span><span class="sxs-lookup"><span data-stu-id="07887-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="07887-299">向 Pages/Shared/_Layout.cshtml  添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="07887-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="07887-300">[布局](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="07887-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="07887-301">控制每个页面的布局（页面选择退出布局时除外）。</span><span class="sxs-lookup"><span data-stu-id="07887-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="07887-302">导入 HTML 结构，例如 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="07887-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="07887-303">调用 `@RenderBody()` 时，呈现 Razor page 的内容。</span><span class="sxs-lookup"><span data-stu-id="07887-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="07887-304">有关详细信息，请参阅[布局页面](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="07887-304">For more information, see [layout page](xref:mvc/views/layout)..</span></span>

<span data-ttu-id="07887-305">在 Pages/_ViewStart.cshtml  中设置 [Layout](xref:mvc/views/layout#specifying-a-layout) 属性：</span><span class="sxs-lookup"><span data-stu-id="07887-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="07887-306"> 布局位于“页面/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="07887-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="07887-307">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="07887-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="07887-308">可以从“页面/共享”  文件夹下的任意 Razor 页面使用“页面”  文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="07887-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="07887-309">布局文件应位于 Pages/Shared  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="07887-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="07887-310">建议不要  将布局文件放在“视图/共享”  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="07887-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="07887-311">视图/共享  是一种 MVC 视图模式。</span><span class="sxs-lookup"><span data-stu-id="07887-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="07887-312">Razor 页面旨在依赖文件夹层次结构，而非路径约定。</span><span class="sxs-lookup"><span data-stu-id="07887-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="07887-313">Razor 页面中的视图搜索包含“页面”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="07887-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="07887-314">用于 MVC 控制器和传统 Razor 视图的布局、模板和分区可直接工作  。</span><span class="sxs-lookup"><span data-stu-id="07887-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="07887-315">添加 Pages/_ViewImports.cshtml  文件：</span><span class="sxs-lookup"><span data-stu-id="07887-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="07887-316">本教程的后续部分中将介绍 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="07887-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="07887-317">`@addTagHelper` 指令将[内置标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/Index)引入“页面”  文件夹中的所有页面。</span><span class="sxs-lookup"><span data-stu-id="07887-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="07887-318">页面上设置的 `@namespace` 指令：</span><span class="sxs-lookup"><span data-stu-id="07887-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="07887-319">`@namespace` 指令将为页面设置命名空间。</span><span class="sxs-lookup"><span data-stu-id="07887-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="07887-320">`@model` 指令无需包含命名空间。</span><span class="sxs-lookup"><span data-stu-id="07887-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="07887-321">_ViewImports.cshtml  中包含 `@namespace` 指令后，指定的命名空间将为在导入 `@namespace` 指令的页面中生成的命名空间提供前缀。</span><span class="sxs-lookup"><span data-stu-id="07887-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="07887-322">生成的命名空间的剩余部分（后缀部分）是包含 _ViewImports.cshtml  的文件夹与包含页面的文件夹之间以点分隔的相对路径。</span><span class="sxs-lookup"><span data-stu-id="07887-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="07887-323">例如，`PageModel` 类 Pages/Customers/Edit.cshtml.cs  显式设置命名空间：</span><span class="sxs-lookup"><span data-stu-id="07887-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="07887-324">Pages/_ViewImports.cshtml  文件设置以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="07887-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="07887-325">为 Pages/Customers/Edit.cshtml  Razor 页面生成的命名空间与 `PageModel` 类相同。</span><span class="sxs-lookup"><span data-stu-id="07887-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="07887-326">`@namespace`  也可用于传统的 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="07887-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="07887-327">考虑 Pages/Create.cshtml  视图文件：[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]</span><span class="sxs-lookup"><span data-stu-id="07887-327">Consider the *Pages/Create.cshtml* view file: [!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]</span></span>

<span data-ttu-id="07887-328">包含 _ViewImports.cshtml 的已更新的 Pages/Create.cshtml 视图文件和前面的布局文件   ：</span><span class="sxs-lookup"><span data-stu-id="07887-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="07887-329">在前面的代码中，_ViewImports.cshtml 导入了命名空间和标记帮助程序  。</span><span class="sxs-lookup"><span data-stu-id="07887-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="07887-330">布局文件导入了 JavaScript 文件。</span><span class="sxs-lookup"><span data-stu-id="07887-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="07887-331">[Razor 页面初学者项目](#rpvs17)包含 Pages/_ValidationScriptsPartial.cshtml  ，它与客户端验证联合。</span><span class="sxs-lookup"><span data-stu-id="07887-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="07887-332">有关分部视图的详细信息，请参阅 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="07887-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="07887-333">页面的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="07887-333">URL generation for Pages</span></span>

<span data-ttu-id="07887-334">之前显示的 `Create` 页面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="07887-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="07887-335">应用具有以下文件/文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="07887-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="07887-336">/Pages </span><span class="sxs-lookup"><span data-stu-id="07887-336">*/Pages*</span></span>

  * <span data-ttu-id="07887-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="07887-338">Privacy.cshtml </span><span class="sxs-lookup"><span data-stu-id="07887-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="07887-339">/Customers </span><span class="sxs-lookup"><span data-stu-id="07887-339">*/Customers*</span></span>

    * <span data-ttu-id="07887-340">Create.cshtml </span><span class="sxs-lookup"><span data-stu-id="07887-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="07887-341">Edit.cshtml </span><span class="sxs-lookup"><span data-stu-id="07887-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="07887-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-342">*Index.cshtml*</span></span>

<span data-ttu-id="07887-343">成功后，Pages/Customers/Create.cshtml and Pages/Customers/Edit.cshtml 页面将重定向到 Pages/Customers/Index.cshtml    。</span><span class="sxs-lookup"><span data-stu-id="07887-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="07887-344">字符串 `./Index` 是用于访问前一页的相对页名称。</span><span class="sxs-lookup"><span data-stu-id="07887-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="07887-345">它用于生成 *Pages/Customers/Index.cshtml* 页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="07887-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="07887-346">例如:</span><span class="sxs-lookup"><span data-stu-id="07887-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="07887-347">绝对页名称 `/Index` 用于生成 *Pages/Index. cshtml* 页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="07887-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="07887-348">例如:</span><span class="sxs-lookup"><span data-stu-id="07887-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="07887-349">页面名称是从根“/Pages”  文件夹到页面的路径（包含前导 `/`，例如 `/Index`）。</span><span class="sxs-lookup"><span data-stu-id="07887-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="07887-350">与硬编码 URL 相比，前面的 URL 生成示例提供了改进的选项和功能。</span><span class="sxs-lookup"><span data-stu-id="07887-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="07887-351">URL 生成使用[路由](xref:mvc/controllers/routing)，并且可以根据目标路径定义路由的方式生成参数并对参数编码。</span><span class="sxs-lookup"><span data-stu-id="07887-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="07887-352">页面的 URL 生成支持相对名称。</span><span class="sxs-lookup"><span data-stu-id="07887-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="07887-353">下表显示了 Pages/Customers/Create.cshtml  中不同的 `RedirectToPage` 参数选择的索引页。</span><span class="sxs-lookup"><span data-stu-id="07887-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="07887-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="07887-354">RedirectToPage(x)</span></span>| <span data-ttu-id="07887-355">页面</span><span class="sxs-lookup"><span data-stu-id="07887-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="07887-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="07887-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="07887-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-357">*Pages/Index*</span></span> |
| <span data-ttu-id="07887-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="07887-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="07887-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="07887-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="07887-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="07887-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-361">*Pages/Index*</span></span> |
| <span data-ttu-id="07887-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="07887-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="07887-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="07887-364">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是相对名称  。</span><span class="sxs-lookup"><span data-stu-id="07887-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="07887-365">结合  `RedirectToPage` 参数与当前页的路径来计算目标页面的名称。</span><span class="sxs-lookup"><span data-stu-id="07887-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="07887-366">构建结构复杂的站点时，相对名称链接很有用。</span><span class="sxs-lookup"><span data-stu-id="07887-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="07887-367">如果使用相对名称链接文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="07887-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="07887-368">重命名文件夹不会破坏相对链接。</span><span class="sxs-lookup"><span data-stu-id="07887-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="07887-369">链接不会中断，因为它们不包含文件夹名称。</span><span class="sxs-lookup"><span data-stu-id="07887-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="07887-370">若要重定向到不同[区域](xref:mvc/controllers/areas)中的页面，请指定区域：</span><span class="sxs-lookup"><span data-stu-id="07887-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="07887-371">有关详细信息，请参阅 <xref:mvc/controllers/areas> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="07887-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="07887-372">ViewData 特性</span><span class="sxs-lookup"><span data-stu-id="07887-372">ViewData attribute</span></span>

<span data-ttu-id="07887-373">可以通过 <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> 将数据传递到页面。</span><span class="sxs-lookup"><span data-stu-id="07887-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="07887-374">具有 [ViewData] 特性的属性从 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> 保存和加载值。</span><span class="sxs-lookup"><span data-stu-id="07887-374">Properties with the [ViewData] attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="07887-375">在下面的示例中，`AboutModel` 将 `[ViewData]` 特性应用于 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="07887-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="07887-376">在“关于”页面中，以模型属性的形式访问 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="07887-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="07887-377">在布局中，从 ViewData 字典读取标题：</span><span class="sxs-lookup"><span data-stu-id="07887-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="07887-378">TempData</span><span class="sxs-lookup"><span data-stu-id="07887-378">TempData</span></span>

<span data-ttu-id="07887-379">ASP.NET Core 公开 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>。</span><span class="sxs-lookup"><span data-stu-id="07887-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="07887-380">此属性存储未读取的数据。</span><span class="sxs-lookup"><span data-stu-id="07887-380">This property stores data until it's read.</span></span> <span data-ttu-id="07887-381"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> 和 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="07887-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="07887-382">`TempData` 在多个请求需要数据的情况下对重定向很有用。</span><span class="sxs-lookup"><span data-stu-id="07887-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="07887-383">下面的代码使用 `TempData` 设置 `Message` 的值：</span><span class="sxs-lookup"><span data-stu-id="07887-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="07887-384">Pages/Customers/Index.cshtml  文件中的以下标记使用 `TempData` 显示 `Message` 的值。</span><span class="sxs-lookup"><span data-stu-id="07887-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="07887-385">Pages/Customers/Index.cshtml.cs 页面模型将 `[TempData]` 属性应用到 `Message` 属性  。</span><span class="sxs-lookup"><span data-stu-id="07887-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="07887-386">有关详细信息，请参阅 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="07887-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="07887-387">针对一个页面的多个处理程序</span><span class="sxs-lookup"><span data-stu-id="07887-387">Multiple handlers per page</span></span>

<span data-ttu-id="07887-388">以下页面使用 `asp-page-handler` 标记帮助程序为两个处理程序生成标记：</span><span class="sxs-lookup"><span data-stu-id="07887-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="07887-389">前面示例中的窗体包含两个提交按钮，每个提交按钮均使用 `FormActionTagHelper` 提交到不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="07887-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="07887-390">`asp-page-handler` 是 `asp-page` 的配套属性。</span><span class="sxs-lookup"><span data-stu-id="07887-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="07887-391">`asp-page-handler` 生成提交到页面定义的各个处理程序方法的 URL。</span><span class="sxs-lookup"><span data-stu-id="07887-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="07887-392">未指定 `asp-page`，因为示例已链接到当前页面。</span><span class="sxs-lookup"><span data-stu-id="07887-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="07887-393">页面模型：</span><span class="sxs-lookup"><span data-stu-id="07887-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="07887-394">前面的代码使用已命名处理程序方法  。</span><span class="sxs-lookup"><span data-stu-id="07887-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="07887-395">已命名处理程序方法通过采用名称中 `On<HTTP Verb>` 之后及 `Async` 之前的文本（如果有）创建。</span><span class="sxs-lookup"><span data-stu-id="07887-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="07887-396">在前面的示例中，页面方法是 OnPost**JoinList**Async 和 OnPost**JoinListUC**Async。</span><span class="sxs-lookup"><span data-stu-id="07887-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="07887-397">删除 OnPost  和 Async  后，处理程序名称为 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="07887-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="07887-398">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="07887-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="07887-399">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="07887-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="07887-400">自定义路由</span><span class="sxs-lookup"><span data-stu-id="07887-400">Custom routes</span></span>

<span data-ttu-id="07887-401">使用 `@page` 指令，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="07887-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="07887-402">指定页面的自定义路由。</span><span class="sxs-lookup"><span data-stu-id="07887-402">Specify a custom route to a page.</span></span> <span data-ttu-id="07887-403">例如，使用 `@page "/Some/Other/Path"` 将“关于”页面的路由设置为 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="07887-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="07887-404">将段追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="07887-404">Append segments to a page's default route.</span></span> <span data-ttu-id="07887-405">例如，可使用 `@page "item"` 将“item”段添加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="07887-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="07887-406">将参数追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="07887-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="07887-407">例如，`@page "{id}"` 页面需要 ID 参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="07887-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="07887-408">支持开头处以波形符 (`~`) 指定的相对于根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="07887-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="07887-409">例如，`@page "~/Some/Other/Path"` 和 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="07887-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="07887-410">可以通过指定路由模板 `@page "{handler?}"`，将 URL 中的查询字符串 `?handler=JoinList` 更改为路由段 `/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="07887-410">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="07887-411">如果你不喜欢 URL 中的查询字符串 `?handler=JoinList`，可以更改路由，将处理程序名称放在 URL 的路径部分。</span><span class="sxs-lookup"><span data-stu-id="07887-411">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="07887-412">可以通过在 `@page` 指令后面添加使用双引号括起来的路由模板来自定义路由。</span><span class="sxs-lookup"><span data-stu-id="07887-412">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="07887-413">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="07887-413">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="07887-414">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="07887-414">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="07887-415">`handler` 前面的 `?` 表示路由参数为可选。</span><span class="sxs-lookup"><span data-stu-id="07887-415">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="07887-416">高级配置和设置</span><span class="sxs-lookup"><span data-stu-id="07887-416">Advanced configuration and settings</span></span>

<span data-ttu-id="07887-417">大多数应用不需要以下部分中的配置和设置。</span><span class="sxs-lookup"><span data-stu-id="07887-417">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="07887-418">要配置高级选项，请使用扩展方法 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>：</span><span class="sxs-lookup"><span data-stu-id="07887-418">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="07887-419">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> 设置页面的根目录，或者为页面添加应用程序模型约定。</span><span class="sxs-lookup"><span data-stu-id="07887-419">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="07887-420">有关约定的详细信息，请参阅[ Razor Pages 约定](xref:security/authorization/razor-pages-authorization)。</span><span class="sxs-lookup"><span data-stu-id="07887-420">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="07887-421">若要预编译视图，请参阅 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="07887-421">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="07887-422">指定 Razor 页面位于内容根目录中</span><span class="sxs-lookup"><span data-stu-id="07887-422">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="07887-423">默认情况下，Razor 页面位于 /Pages 目录的根位置  。</span><span class="sxs-lookup"><span data-stu-id="07887-423">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="07887-424">添加 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> 以指定 Razor Pages 位于应用的内容根目录 (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) 中：</span><span class="sxs-lookup"><span data-stu-id="07887-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the content root (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="07887-425">指定 Razor 页面位于自定义根目录中</span><span class="sxs-lookup"><span data-stu-id="07887-425">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="07887-426">添加 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>，以指定 Razor 页面位于应用中自定义根目录位置（提供相对路径）：</span><span class="sxs-lookup"><span data-stu-id="07887-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="07887-427">其他资源</span><span class="sxs-lookup"><span data-stu-id="07887-427">Additional resources</span></span>

* <span data-ttu-id="07887-428">请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)，这篇文章以本文为基础编写。</span><span class="sxs-lookup"><span data-stu-id="07887-428">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="07887-429">[下载或查看示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span><span class="sxs-lookup"><span data-stu-id="07887-429">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07887-430">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="07887-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="07887-431">Razor 页面是 ASP.NET Core MVC 的一个新特性，它可以使基于页面的编码方式更简单高效。</span><span class="sxs-lookup"><span data-stu-id="07887-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="07887-432">若要查找使用模型视图控制器方法的教程，请参阅 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="07887-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="07887-433">本文档介绍 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="07887-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="07887-434">它并不是分步教程。</span><span class="sxs-lookup"><span data-stu-id="07887-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="07887-435">如果认为某些部分过于复杂，请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="07887-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="07887-436">有关 ASP.NET Core 的概述，请参阅 [ASP.NET Core 简介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="07887-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="07887-437">系统必备</span><span class="sxs-lookup"><span data-stu-id="07887-437">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07887-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07887-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07887-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07887-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07887-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="07887-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="07887-441">创建 Razor Pages 项目</span><span class="sxs-lookup"><span data-stu-id="07887-441">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07887-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07887-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07887-443">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何创建 Razor Pages 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="07887-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07887-444">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="07887-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="07887-445">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="07887-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="07887-446">在 Visual Studio for Mac 中打开生成的 .csproj  文件。</span><span class="sxs-lookup"><span data-stu-id="07887-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07887-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07887-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07887-448">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="07887-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="07887-449">Razor 页面</span><span class="sxs-lookup"><span data-stu-id="07887-449">Razor Pages</span></span>

<span data-ttu-id="07887-450">Startup.cs 中已启用 Razor 页面  ：</span><span class="sxs-lookup"><span data-stu-id="07887-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="07887-451">请考虑一个基本页面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="07887-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="07887-452">前面的代码与具有控制器和视图的 ASP.NET Core 应用中使用的 [Razor 视图文件](xref:tutorials/first-mvc-app/adding-view)非常相似。</span><span class="sxs-lookup"><span data-stu-id="07887-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="07887-453">不同之处在于 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="07887-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="07887-454">`@page` 使文件转换为一个 MVC 操作 ，这意味着它将直接处理请求，而无需通过控制器处理。</span><span class="sxs-lookup"><span data-stu-id="07887-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="07887-455">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="07887-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="07887-456">`@page` 将影响其他 Razor 构造的行为。</span><span class="sxs-lookup"><span data-stu-id="07887-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="07887-457">将在以下两个文件中显示使用 `PageModel` 类的类似页面。</span><span class="sxs-lookup"><span data-stu-id="07887-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="07887-458">Pages/Index2.cshtml  文件：</span><span class="sxs-lookup"><span data-stu-id="07887-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="07887-459">Pages/Index2.cshtml.cs 页面模型  ：</span><span class="sxs-lookup"><span data-stu-id="07887-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="07887-460">按照惯例，`PageModel` 类文件的名称与追加 .cs  的 Razor 页面文件名称相同。</span><span class="sxs-lookup"><span data-stu-id="07887-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="07887-461">例如，前面的 Razor 页面的名称为 Pages/Index2.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="07887-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="07887-462">包含 `PageModel` 类的文件的名称为 Pages/Index2.cshtml.cs  。</span><span class="sxs-lookup"><span data-stu-id="07887-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="07887-463">页面的 URL 路径的关联由页面在文件系统中的位置决定。</span><span class="sxs-lookup"><span data-stu-id="07887-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="07887-464">下表显示了 Razor 页面路径及匹配的 URL：</span><span class="sxs-lookup"><span data-stu-id="07887-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="07887-465">文件名和路径</span><span class="sxs-lookup"><span data-stu-id="07887-465">File name and path</span></span>               | <span data-ttu-id="07887-466">匹配的 URL</span><span class="sxs-lookup"><span data-stu-id="07887-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="07887-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="07887-468">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="07887-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="07887-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="07887-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="07887-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="07887-472">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="07887-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="07887-473">注意：</span><span class="sxs-lookup"><span data-stu-id="07887-473">Notes:</span></span>

* <span data-ttu-id="07887-474">默认情况下，运行时在“Pages”文件夹中查找 Razor 页面文件。 </span><span class="sxs-lookup"><span data-stu-id="07887-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="07887-475">URL 未包含页面时，`Index` 为默认页面。</span><span class="sxs-lookup"><span data-stu-id="07887-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="07887-476">编写基本窗体</span><span class="sxs-lookup"><span data-stu-id="07887-476">Write a basic form</span></span>

<span data-ttu-id="07887-477">由于 Razor 页面的设计，在构建应用时可轻松实施用于 Web 浏览器的常用模式。</span><span class="sxs-lookup"><span data-stu-id="07887-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="07887-478">[模型绑定](xref:mvc/models/model-binding)、[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 HTML 帮助程序均只可用于 Razor 页面类中定义的属性。 </span><span class="sxs-lookup"><span data-stu-id="07887-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="07887-479">请参考为 `Contact` 模型实现基本的“联系我们”窗体的页面：</span><span class="sxs-lookup"><span data-stu-id="07887-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="07887-480">在本文档中的示例中，`DbContext` 在 [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 文件中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="07887-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="07887-481">数据模型：</span><span class="sxs-lookup"><span data-stu-id="07887-481">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="07887-482">数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="07887-482">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="07887-483">Pages/Create.cshtml  视图文件：</span><span class="sxs-lookup"><span data-stu-id="07887-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="07887-484">Pages/Create.cshtml.cs 页面模型  ：</span><span class="sxs-lookup"><span data-stu-id="07887-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="07887-485">按照惯例，`PageModel` 类命名为 `<PageName>Model`并且它与页面位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="07887-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="07887-486">使用 `PageModel` 类，可以将页面的逻辑与其展示分离开来。</span><span class="sxs-lookup"><span data-stu-id="07887-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="07887-487">它定义了页面处理程序，用于处理发送到页面的请求和用于呈现页面的数据。</span><span class="sxs-lookup"><span data-stu-id="07887-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="07887-488">这种隔离可实现：</span><span class="sxs-lookup"><span data-stu-id="07887-488">This separation allows:</span></span>

* <span data-ttu-id="07887-489">通过[依赖关系注入](xref:fundamentals/dependency-injection)管理页面依赖项。</span><span class="sxs-lookup"><span data-stu-id="07887-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="07887-490">对页面执行[单元测试](xref:test/razor-pages-tests)。</span><span class="sxs-lookup"><span data-stu-id="07887-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="07887-491">页面包含 `OnPostAsync` 处理程序方法  ，它在 `POST` 请求上运行（当用户发布窗体时）。</span><span class="sxs-lookup"><span data-stu-id="07887-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="07887-492">可以为任何 HTTP 谓词添加处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="07887-493">最常见的处理程序是：</span><span class="sxs-lookup"><span data-stu-id="07887-493">The most common handlers are:</span></span>

* <span data-ttu-id="07887-494">`OnGet`，用于初始化页面所需的状态。</span><span class="sxs-lookup"><span data-stu-id="07887-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="07887-495">[OnGet](#OnGet) 示例。</span><span class="sxs-lookup"><span data-stu-id="07887-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="07887-496">`OnPost`，用于处理窗体提交。</span><span class="sxs-lookup"><span data-stu-id="07887-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="07887-497">`Async` 命名后缀为可选，但是按照惯例通常会将它用于异步函数。</span><span class="sxs-lookup"><span data-stu-id="07887-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="07887-498">前面的代码通常用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="07887-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="07887-499">如果你熟悉使用控制器和视图的 ASP.NET 应用：</span><span class="sxs-lookup"><span data-stu-id="07887-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="07887-500">前面示例中的 `OnPostAsync` 代码类似于典型的控制器代码。</span><span class="sxs-lookup"><span data-stu-id="07887-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="07887-501">多数 MVC 基元（例如[模型绑定](xref:mvc/models/model-binding)、[验证](xref:mvc/models/validation)、[验证](xref:mvc/models/validation)和操作结果）都是共享的。</span><span class="sxs-lookup"><span data-stu-id="07887-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="07887-502">之前的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="07887-502">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="07887-503">`OnPostAsync` 的基本流：</span><span class="sxs-lookup"><span data-stu-id="07887-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="07887-504">检查验证错误。</span><span class="sxs-lookup"><span data-stu-id="07887-504">Check for validation errors.</span></span>

* <span data-ttu-id="07887-505">如果没有错误，则保存数据并重定向。</span><span class="sxs-lookup"><span data-stu-id="07887-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="07887-506">如果有错误，则再次显示页面并附带验证消息。</span><span class="sxs-lookup"><span data-stu-id="07887-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="07887-507">客户端验证与传统的 ASP.NET Core MVC 应用程序相同。</span><span class="sxs-lookup"><span data-stu-id="07887-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="07887-508">很多情况下，都会在客户端上检测到验证错误，并且从不将它们提交到服务器。</span><span class="sxs-lookup"><span data-stu-id="07887-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="07887-509">成功输入数据后，`OnPostAsync` 处理程序方法调用 `RedirectToPage` 帮助程序方法来返回 `RedirectToPageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="07887-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="07887-510">`RedirectToPage` 是新的操作结果，类似于 `RedirectToAction` 或 `RedirectToRoute`，但是已针对页面进行自定义。</span><span class="sxs-lookup"><span data-stu-id="07887-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="07887-511">在前面的示例中，它将重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="07887-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="07887-512">[页面 URL 生成](#url_gen)部分中详细介绍了 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="07887-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="07887-513">提交的窗体存在（已传递到服务器的）验证错误时，`OnPostAsync` 处理程序方法调用 `Page` 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="07887-514">`Page` 返回 `PageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="07887-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="07887-515">返回 `Page` 的过程与控制器中的操作返回 `View` 的过程相似。</span><span class="sxs-lookup"><span data-stu-id="07887-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="07887-516">`PageResult` 是处理程序方法的默认返回类型。</span><span class="sxs-lookup"><span data-stu-id="07887-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="07887-517">返回 `void` 的处理程序方法将显示页面。</span><span class="sxs-lookup"><span data-stu-id="07887-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="07887-518">`Customer` 属性使用 `[BindProperty]` 特性来选择加入模型绑定。</span><span class="sxs-lookup"><span data-stu-id="07887-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="07887-519">默认情况下，Razor 页面只绑定带有非 `GET` 谓词的属性。</span><span class="sxs-lookup"><span data-stu-id="07887-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="07887-520">绑定属性可以减少需要编写的代码量。</span><span class="sxs-lookup"><span data-stu-id="07887-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="07887-521">绑定通过使用相同的属性显示窗体字段 (`<input asp-for="Customer.Name">`) 来减少代码，并接受输入。</span><span class="sxs-lookup"><span data-stu-id="07887-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="07887-522">主页 (Index.cshtml  )：</span><span class="sxs-lookup"><span data-stu-id="07887-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="07887-523">关联的 `PageModel` 类 (Index.cshtml.cs)  ：</span><span class="sxs-lookup"><span data-stu-id="07887-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="07887-524">Index.cshtml  文件包含以下标记来创建每个联系人项的编辑链接：</span><span class="sxs-lookup"><span data-stu-id="07887-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="07887-525">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>`[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)使用 `asp-route-{value}` 属性生成“编辑”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="07887-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="07887-526">此链接包含路由数据及联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="07887-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="07887-527">例如 `https://localhost:5001/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="07887-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="07887-528">[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="07887-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="07887-529">标记帮助程序由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 启动</span><span class="sxs-lookup"><span data-stu-id="07887-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="07887-530">Pages/Edit.cshtml  文件：</span><span class="sxs-lookup"><span data-stu-id="07887-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="07887-531">第一行包含 `@page "{id:int}"` 指令。</span><span class="sxs-lookup"><span data-stu-id="07887-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="07887-532">路由约束 `"{id:int}"` 告诉页面接受包含 `int` 路由数据的页面请求。</span><span class="sxs-lookup"><span data-stu-id="07887-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="07887-533">如果页面请求未包含可转换为 `int` 的路由数据，则运行时返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="07887-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="07887-534">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="07887-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="07887-535">Pages/Edit.cshtml.cs  文件：</span><span class="sxs-lookup"><span data-stu-id="07887-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="07887-536">Index.cshtml 文件还包含用于为每个客户联系人创建删除按钮的标记： </span><span class="sxs-lookup"><span data-stu-id="07887-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="07887-537">删除按钮采用 HTML 呈现，其 `formaction` 包括参数：</span><span class="sxs-lookup"><span data-stu-id="07887-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="07887-538">`asp-route-id` 属性指定的客户联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="07887-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="07887-539">`asp-page-handler` 属性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="07887-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="07887-540">下面是呈现的删除按钮的示例，其中客户联系人 ID 为 `1`：</span><span class="sxs-lookup"><span data-stu-id="07887-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="07887-541">选中按钮时，向服务器发送窗体 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="07887-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="07887-542">按照惯例，根据方案 `OnPost[handler]Async` 基于 `handler` 参数的值来选择处理程序方法的名称。</span><span class="sxs-lookup"><span data-stu-id="07887-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="07887-543">因为本示例中 `handler` 是 `delete`，因此 `OnPostDeleteAsync` 处理程序方法用于处理 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="07887-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="07887-544">如果 `asp-page-handler` 设置为其他值（如 `remove`），则选择名称为 `OnPostRemoveAsync` 的处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07887-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="07887-545">以下代码显示了 `OnPostDeleteAsync` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="07887-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="07887-546">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="07887-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="07887-547">接受来自查询字符串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="07887-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="07887-548">如果 Index.cshtml  页面指令包含路由约束 `"{id:int?}"`，则 `id` 来自路由数据。</span><span class="sxs-lookup"><span data-stu-id="07887-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="07887-549">`id` 的路由数据在 `https://localhost:5001/Customers/2` 等 URI 中指定。</span><span class="sxs-lookup"><span data-stu-id="07887-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="07887-550">使用 `FindAsync` 查询客户联系人的数据库。</span><span class="sxs-lookup"><span data-stu-id="07887-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="07887-551">如果找到客户联系人，则从客户联系人列表将其删除。</span><span class="sxs-lookup"><span data-stu-id="07887-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="07887-552">数据库将更新。</span><span class="sxs-lookup"><span data-stu-id="07887-552">The database is updated.</span></span>
* <span data-ttu-id="07887-553">调用 `RedirectToPage`，重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="07887-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="07887-554">按需标记页面属性</span><span class="sxs-lookup"><span data-stu-id="07887-554">Mark page properties as required</span></span>

<span data-ttu-id="07887-555">`PageModel` 上的属性可通过 [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) 特性进行修饰：</span><span class="sxs-lookup"><span data-stu-id="07887-555">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="07887-556">有关详细信息，请参阅[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="07887-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="07887-557">使用 OnGet 处理程序回退来处理 HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="07887-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="07887-558">`HEAD` 请求可检索特定资源的标头。</span><span class="sxs-lookup"><span data-stu-id="07887-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="07887-559">与 `GET` 请求不同，`HEAD` 请求不返回响应正文。</span><span class="sxs-lookup"><span data-stu-id="07887-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="07887-560">通常，针对 `HEAD` 请求创建和调用 `OnHead` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="07887-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="07887-561">在 ASP.NET Core 2.1 或更高版本中，如果未定义 `OnHead` 处理程序，则 Razor Pages 会回退到调用 `OnGet` 处理程序。</span><span class="sxs-lookup"><span data-stu-id="07887-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="07887-562">此行为是通过在 `Startup.ConfigureServices` 中调用 [SetCompatibilityVersion](xref:mvc/compatibility-version) 而启用的：</span><span class="sxs-lookup"><span data-stu-id="07887-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="07887-563">默认模板在 ASP.NET Core 2.1 和 2.2 中生成 `SetCompatibilityVersion` 调用。</span><span class="sxs-lookup"><span data-stu-id="07887-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="07887-564">`SetCompatibilityVersion` 有效地将 Razor 页面选项 `AllowMappingHeadRequestsToGetHandler` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="07887-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="07887-565">可显式选择使用特定行为，而不是通过 `SetCompatibilityVersion` 选择使用所有行为  。</span><span class="sxs-lookup"><span data-stu-id="07887-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="07887-566">通过下列代码，可选择允许将 `HEAD` 请求映射到 `OnGet` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="07887-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="07887-567">XSRF/CSRF 和 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="07887-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="07887-568">无需为[防伪验证](xref:security/anti-request-forgery)编写任何代码。</span><span class="sxs-lookup"><span data-stu-id="07887-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="07887-569">Razor 页面自动将防伪标记生成过程和验证过程包含在内。</span><span class="sxs-lookup"><span data-stu-id="07887-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="07887-570">将布局、分区、模板和标记帮助程序用于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="07887-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="07887-571">页面可使用 Razor 视图引擎的所有功能。</span><span class="sxs-lookup"><span data-stu-id="07887-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="07887-572">布局、分区、模板、标记帮助程序、_ViewStart.cshtml  和 _ViewImports.cshtml  的工作方式与它们在传统的 Razor 视图中的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="07887-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="07887-573">让我们使用其中的一些功能来整理此页面。</span><span class="sxs-lookup"><span data-stu-id="07887-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="07887-574">向 Pages/Shared/_Layout.cshtml  添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="07887-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="07887-575">[布局](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="07887-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="07887-576">控制每个页面的布局（页面选择退出布局时除外）。</span><span class="sxs-lookup"><span data-stu-id="07887-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="07887-577">导入 HTML 结构，例如 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="07887-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="07887-578">请参阅[布局页面](xref:mvc/views/layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="07887-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="07887-579">在 Pages/_ViewStart.cshtml  中设置 [Layout](xref:mvc/views/layout#specifying-a-layout) 属性：</span><span class="sxs-lookup"><span data-stu-id="07887-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="07887-580"> 布局位于“页面/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="07887-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="07887-581">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="07887-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="07887-582">可以从“页面/共享”  文件夹下的任意 Razor 页面使用“页面”  文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="07887-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="07887-583">布局文件应位于 Pages/Shared  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="07887-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="07887-584">建议不要  将布局文件放在“视图/共享”  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="07887-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="07887-585">视图/共享  是一种 MVC 视图模式。</span><span class="sxs-lookup"><span data-stu-id="07887-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="07887-586">Razor 页面旨在依赖文件夹层次结构，而非路径约定。</span><span class="sxs-lookup"><span data-stu-id="07887-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="07887-587">Razor 页面中的视图搜索包含“页面”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="07887-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="07887-588">用于 MVC 控制器和传统 Razor 视图的布局、模板和分区可直接工作  。</span><span class="sxs-lookup"><span data-stu-id="07887-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="07887-589">添加 Pages/_ViewImports.cshtml  文件：</span><span class="sxs-lookup"><span data-stu-id="07887-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="07887-590">本教程的后续部分中将介绍 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="07887-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="07887-591">`@addTagHelper` 指令将[内置标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/Index)引入“页面”  文件夹中的所有页面。</span><span class="sxs-lookup"><span data-stu-id="07887-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="07887-592">页面上显式使用 `@namespace` 指令后：</span><span class="sxs-lookup"><span data-stu-id="07887-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="07887-593">此指令将为页面设置命名空间。</span><span class="sxs-lookup"><span data-stu-id="07887-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="07887-594">`@model` 指令无需包含命名空间。</span><span class="sxs-lookup"><span data-stu-id="07887-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="07887-595">_ViewImports.cshtml  中包含 `@namespace` 指令后，指定的命名空间将为在导入 `@namespace` 指令的页面中生成的命名空间提供前缀。</span><span class="sxs-lookup"><span data-stu-id="07887-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="07887-596">生成的命名空间的剩余部分（后缀部分）是包含 _ViewImports.cshtml  的文件夹与包含页面的文件夹之间以点分隔的相对路径。</span><span class="sxs-lookup"><span data-stu-id="07887-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="07887-597">例如，`PageModel` 类 Pages/Customers/Edit.cshtml.cs  显式设置命名空间：</span><span class="sxs-lookup"><span data-stu-id="07887-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="07887-598">Pages/_ViewImports.cshtml  文件设置以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="07887-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="07887-599">为 Pages/Customers/Edit.cshtml  Razor 页面生成的命名空间与 `PageModel` 类相同。</span><span class="sxs-lookup"><span data-stu-id="07887-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="07887-600">`@namespace`  也可用于传统的 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="07887-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="07887-601">原始的 Pages/Create.cshtml  视图文件：</span><span class="sxs-lookup"><span data-stu-id="07887-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="07887-602">更新后的 Pages/Create.cshtml  视图文件：</span><span class="sxs-lookup"><span data-stu-id="07887-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="07887-603">[Razor 页面初学者项目](#rpvs17)包含 Pages/_ValidationScriptsPartial.cshtml  ，它与客户端验证联合。</span><span class="sxs-lookup"><span data-stu-id="07887-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="07887-604">有关分部视图的详细信息，请参阅 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="07887-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="07887-605">页面的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="07887-605">URL generation for Pages</span></span>

<span data-ttu-id="07887-606">之前显示的 `Create` 页面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="07887-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="07887-607">应用具有以下文件/文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="07887-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="07887-608">/Pages </span><span class="sxs-lookup"><span data-stu-id="07887-608">*/Pages*</span></span>

  * <span data-ttu-id="07887-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="07887-610">/Customers </span><span class="sxs-lookup"><span data-stu-id="07887-610">*/Customers*</span></span>

    * <span data-ttu-id="07887-611">Create.cshtml </span><span class="sxs-lookup"><span data-stu-id="07887-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="07887-612">Edit.cshtml </span><span class="sxs-lookup"><span data-stu-id="07887-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="07887-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="07887-613">*Index.cshtml*</span></span>

<span data-ttu-id="07887-614">成功后，Pages/Customers/Create.cshtml  和 Pages/Customers/Edit.cshtml  页面将重定向到 Pages/Index.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="07887-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="07887-615">字符串 `/Index` 是用于访问上一页的 URI 的组成部分。</span><span class="sxs-lookup"><span data-stu-id="07887-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="07887-616">可以使用字符串 `/Index` 生成 Pages/Index.cshtml  页面的 URI。</span><span class="sxs-lookup"><span data-stu-id="07887-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="07887-617">例如:</span><span class="sxs-lookup"><span data-stu-id="07887-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="07887-618">页面名称是从根“/Pages”  文件夹到页面的路径（包含前导 `/`，例如 `/Index`）。</span><span class="sxs-lookup"><span data-stu-id="07887-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="07887-619">与硬编码 URL 相比，前面的 URL 生成示例提供了改进的选项和功能。</span><span class="sxs-lookup"><span data-stu-id="07887-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="07887-620">URL 生成使用[路由](xref:mvc/controllers/routing)，并且可以根据目标路径定义路由的方式生成参数并对参数编码。</span><span class="sxs-lookup"><span data-stu-id="07887-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="07887-621">页面的 URL 生成支持相对名称。</span><span class="sxs-lookup"><span data-stu-id="07887-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="07887-622">下表显示了 Pages/Customers/Create.cshtml  中不同的 `RedirectToPage` 参数选择的索引页：</span><span class="sxs-lookup"><span data-stu-id="07887-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="07887-623">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="07887-623">RedirectToPage(x)</span></span>| <span data-ttu-id="07887-624">页面</span><span class="sxs-lookup"><span data-stu-id="07887-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="07887-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="07887-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="07887-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-626">*Pages/Index*</span></span> |
| <span data-ttu-id="07887-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="07887-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="07887-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="07887-629">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="07887-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="07887-630">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-630">*Pages/Index*</span></span> |
| <span data-ttu-id="07887-631">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="07887-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="07887-632">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="07887-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="07887-633">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是相对名称  。</span><span class="sxs-lookup"><span data-stu-id="07887-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="07887-634">结合  `RedirectToPage` 参数与当前页的路径来计算目标页面的名称。</span><span class="sxs-lookup"><span data-stu-id="07887-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="07887-635">构建结构复杂的站点时，相对名称链接很有用。</span><span class="sxs-lookup"><span data-stu-id="07887-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="07887-636">如果使用相对名称链接文件夹中的页面，则可以重命名该文件夹。</span><span class="sxs-lookup"><span data-stu-id="07887-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="07887-637">所有链接仍然有效（因为这些链接未包含此文件夹名称）。</span><span class="sxs-lookup"><span data-stu-id="07887-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="07887-638">若要重定向到不同[区域](xref:mvc/controllers/areas)中的页面，请指定区域：</span><span class="sxs-lookup"><span data-stu-id="07887-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="07887-639">有关详细信息，请参阅 <xref:mvc/controllers/areas>。</span><span class="sxs-lookup"><span data-stu-id="07887-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="07887-640">ViewData 特性</span><span class="sxs-lookup"><span data-stu-id="07887-640">ViewData attribute</span></span>

<span data-ttu-id="07887-641">可以通过 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) 将数据传递到页面。</span><span class="sxs-lookup"><span data-stu-id="07887-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="07887-642">控制器或 Razor 页面模型上使用 `[ViewData]` 修饰的属性将其值存储在 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 中并从此处进行加载。</span><span class="sxs-lookup"><span data-stu-id="07887-642">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="07887-643">在下面的示例中，`AboutModel` 包含使用 `[ViewData]` 修饰的 `Title` 属性。</span><span class="sxs-lookup"><span data-stu-id="07887-643">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="07887-644">`Title` 属性设置为“关于”页面的标题：</span><span class="sxs-lookup"><span data-stu-id="07887-644">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="07887-645">在“关于”页面中，以模型属性的形式访问 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="07887-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="07887-646">在布局中，从 ViewData 字典读取标题：</span><span class="sxs-lookup"><span data-stu-id="07887-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="07887-647">TempData</span><span class="sxs-lookup"><span data-stu-id="07887-647">TempData</span></span>

<span data-ttu-id="07887-648">ASP.NET 在[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)上公开了 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) 属性。</span><span class="sxs-lookup"><span data-stu-id="07887-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="07887-649">此属性存储未读取的数据。</span><span class="sxs-lookup"><span data-stu-id="07887-649">This property stores data until it's read.</span></span> <span data-ttu-id="07887-650">`Keep` 和 `Peek` 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="07887-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="07887-651">多个请求需要数据时，`TempData` 有助于进行重定向。</span><span class="sxs-lookup"><span data-stu-id="07887-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="07887-652">下面的代码使用 `TempData` 设置 `Message` 的值：</span><span class="sxs-lookup"><span data-stu-id="07887-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="07887-653">Pages/Customers/Index.cshtml  文件中的以下标记使用 `TempData` 显示 `Message` 的值。</span><span class="sxs-lookup"><span data-stu-id="07887-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="07887-654">Pages/Customers/Index.cshtml.cs 页面模型将 `[TempData]` 属性应用到 `Message` 属性  。</span><span class="sxs-lookup"><span data-stu-id="07887-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="07887-655">有关详细信息，请参阅 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="07887-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="07887-656">针对一个页面的多个处理程序</span><span class="sxs-lookup"><span data-stu-id="07887-656">Multiple handlers per page</span></span>

<span data-ttu-id="07887-657">以下页面使用 `asp-page-handler` 标记帮助程序为两个处理程序生成标记：</span><span class="sxs-lookup"><span data-stu-id="07887-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="07887-658">前面示例中的窗体包含两个提交按钮，每个提交按钮均使用 `FormActionTagHelper` 提交到不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="07887-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="07887-659">`asp-page-handler` 是 `asp-page` 的配套属性。</span><span class="sxs-lookup"><span data-stu-id="07887-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="07887-660">`asp-page-handler` 生成提交到页面定义的各个处理程序方法的 URL。</span><span class="sxs-lookup"><span data-stu-id="07887-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="07887-661">未指定 `asp-page`，因为示例已链接到当前页面。</span><span class="sxs-lookup"><span data-stu-id="07887-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="07887-662">页面模型：</span><span class="sxs-lookup"><span data-stu-id="07887-662">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="07887-663">前面的代码使用已命名处理程序方法  。</span><span class="sxs-lookup"><span data-stu-id="07887-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="07887-664">已命名处理程序方法通过采用名称中 `On<HTTP Verb>` 之后及 `Async` 之前的文本（如果有）创建。</span><span class="sxs-lookup"><span data-stu-id="07887-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="07887-665">在前面的示例中，页面方法是 OnPost**JoinList**Async 和 OnPost**JoinListUC**Async。</span><span class="sxs-lookup"><span data-stu-id="07887-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="07887-666">删除 OnPost  和 Async  后，处理程序名称为 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="07887-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="07887-667">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="07887-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="07887-668">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="07887-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="07887-669">自定义路由</span><span class="sxs-lookup"><span data-stu-id="07887-669">Custom routes</span></span>

<span data-ttu-id="07887-670">使用 `@page` 指令，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="07887-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="07887-671">指定页面的自定义路由。</span><span class="sxs-lookup"><span data-stu-id="07887-671">Specify a custom route to a page.</span></span> <span data-ttu-id="07887-672">例如，使用 `@page "/Some/Other/Path"` 将“关于”页面的路由设置为 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="07887-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="07887-673">将段追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="07887-673">Append segments to a page's default route.</span></span> <span data-ttu-id="07887-674">例如，可使用 `@page "item"` 将“item”段添加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="07887-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="07887-675">将参数追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="07887-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="07887-676">例如，`@page "{id}"` 页面需要 ID 参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="07887-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="07887-677">支持开头处以波形符 (`~`) 指定的相对于根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="07887-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="07887-678">例如，`@page "~/Some/Other/Path"` 和 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="07887-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="07887-679">可以通过指定路由模板 `@page "{handler?}"`，将 URL 中的查询字符串 `?handler=JoinList` 更改为路由段 `/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="07887-679">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="07887-680">如果你不喜欢 URL 中的查询字符串 `?handler=JoinList`，可以更改路由，将处理程序名称放在 URL 的路径部分。</span><span class="sxs-lookup"><span data-stu-id="07887-680">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="07887-681">可以通过在 `@page` 指令后面添加使用双引号括起来的路由模板来自定义路由。</span><span class="sxs-lookup"><span data-stu-id="07887-681">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="07887-682">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="07887-682">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="07887-683">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="07887-683">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="07887-684">`handler` 前面的 `?` 表示路由参数为可选。</span><span class="sxs-lookup"><span data-stu-id="07887-684">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="07887-685">配置和设置</span><span class="sxs-lookup"><span data-stu-id="07887-685">Configuration and settings</span></span>

<span data-ttu-id="07887-686">若要配置高级选项，请在 MVC 生成器上使用 `AddRazorPagesOptions` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="07887-686">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="07887-687">目前，可以使用 `RazorPagesOptions` 设置页面的根目录，或者为页面添加应用程序模型约定。</span><span class="sxs-lookup"><span data-stu-id="07887-687">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="07887-688">通过这种方式，我们在将来会实现更多扩展功能。</span><span class="sxs-lookup"><span data-stu-id="07887-688">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="07887-689">若要预编译视图，请参阅 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="07887-689">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="07887-690">[下载或查看示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="07887-690">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="07887-691">请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)，这篇文章以本文为基础编写。</span><span class="sxs-lookup"><span data-stu-id="07887-691">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="07887-692">指定 Razor 页面位于内容根目录中</span><span class="sxs-lookup"><span data-stu-id="07887-692">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="07887-693">默认情况下，Razor 页面位于 /Pages 目录的根位置  。</span><span class="sxs-lookup"><span data-stu-id="07887-693">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="07887-694">向 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 添加 [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot)，以指定 Razor 页面位于应用的内容根目录 ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) 中：</span><span class="sxs-lookup"><span data-stu-id="07887-694">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="07887-695">指定 Razor 页面位于自定义根目录中</span><span class="sxs-lookup"><span data-stu-id="07887-695">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="07887-696">向 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 添加 [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot)，以指定 Razor 页面位于应用中自定义根目录位置（提供相对路径）：</span><span class="sxs-lookup"><span data-stu-id="07887-696">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="07887-697">其他资源</span><span class="sxs-lookup"><span data-stu-id="07887-697">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
