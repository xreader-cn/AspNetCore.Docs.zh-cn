---
title: ASP.NET Core 中的 Razor 页面介绍
author: Rick-Anderson
description: 了解 ASP.NET Core 中的 Razor 页面如何使基于页面的编码方式比使用 MVC 更简单高效。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 7df57153efc58b6a19ce663eb31d173da11b1005
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64881992"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="17a72-103">ASP.NET Core 中的 Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="17a72-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="17a72-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="17a72-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="17a72-105">Razor 页面是 ASP.NET Core MVC 的一个新特性，它可以使基于页面的编码方式更简单高效。</span><span class="sxs-lookup"><span data-stu-id="17a72-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="17a72-106">若要查找使用模型视图控制器方法的教程，请参阅 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="17a72-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="17a72-107">本文档介绍 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="17a72-108">它并不是分步教程。</span><span class="sxs-lookup"><span data-stu-id="17a72-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="17a72-109">如果认为某些部分过于复杂，请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="17a72-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="17a72-110">有关 ASP.NET Core 的概述，请参阅 [ASP.NET Core 简介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="17a72-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="17a72-111">系统必备</span><span class="sxs-lookup"><span data-stu-id="17a72-111">Prerequisites</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="17a72-112">创建 Razor Pages 项目</span><span class="sxs-lookup"><span data-stu-id="17a72-112">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17a72-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17a72-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17a72-114">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何创建 Razor Pages 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="17a72-114">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="17a72-115">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="17a72-115">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="17a72-116">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="17a72-116">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="17a72-117">在命令行中运行 `dotnet new razor`。</span><span class="sxs-lookup"><span data-stu-id="17a72-117">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="17a72-118">在 Visual Studio for Mac 中打开生成的 .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="17a72-118">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="17a72-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17a72-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="17a72-120">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="17a72-120">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="17a72-121">在命令行中运行 `dotnet new razor`。</span><span class="sxs-lookup"><span data-stu-id="17a72-121">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="17a72-122">Razor 页面</span><span class="sxs-lookup"><span data-stu-id="17a72-122">Razor Pages</span></span>

<span data-ttu-id="17a72-123">Startup.cs 中已启用 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="17a72-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="17a72-124">请考虑一个基本页面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="17a72-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="17a72-125">前面的代码与具有控制器和视图的 ASP.NET Core 应用中使用的 [Razor 视图文件](xref:tutorials/first-mvc-app/adding-view)非常相似。</span><span class="sxs-lookup"><span data-stu-id="17a72-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="17a72-126">不同之处在于 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="17a72-126">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="17a72-127">`@page` 使文件转换为一个 MVC 操作 ，这意味着它将直接处理请求，而无需通过控制器处理。</span><span class="sxs-lookup"><span data-stu-id="17a72-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="17a72-128">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="17a72-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="17a72-129">`@page` 将影响其他 Razor 构造的行为。</span><span class="sxs-lookup"><span data-stu-id="17a72-129">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="17a72-130">将在以下两个文件中显示使用 `PageModel` 类的类似页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-130">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="17a72-131">Pages/Index2.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-131">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="17a72-132">Pages/Index2.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="17a72-132">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="17a72-133">按照惯例，`PageModel` 类文件的名称与追加 .cs 的 Razor 页面文件名称相同。</span><span class="sxs-lookup"><span data-stu-id="17a72-133">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="17a72-134">例如，前面的 Razor 页面的名称为 Pages/Index2.cshtml。</span><span class="sxs-lookup"><span data-stu-id="17a72-134">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="17a72-135">包含 `PageModel` 类的文件的名称为 Pages/Index2.cshtml.cs。</span><span class="sxs-lookup"><span data-stu-id="17a72-135">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="17a72-136">页面的 URL 路径的关联由页面在文件系统中的位置决定。</span><span class="sxs-lookup"><span data-stu-id="17a72-136">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="17a72-137">下表显示了 Razor 页面路径及匹配的 URL：</span><span class="sxs-lookup"><span data-stu-id="17a72-137">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="17a72-138">文件名和路径</span><span class="sxs-lookup"><span data-stu-id="17a72-138">File name and path</span></span>               | <span data-ttu-id="17a72-139">匹配的 URL</span><span class="sxs-lookup"><span data-stu-id="17a72-139">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="17a72-140">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="17a72-140">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="17a72-141">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="17a72-141">`/` or `/Index`</span></span> |
| <span data-ttu-id="17a72-142">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="17a72-142">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="17a72-143">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="17a72-143">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="17a72-144">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="17a72-144">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="17a72-145">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="17a72-145">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="17a72-146">注意：</span><span class="sxs-lookup"><span data-stu-id="17a72-146">Notes:</span></span>

* <span data-ttu-id="17a72-147">默认情况下，运行时在“Pages”文件夹中查找 Razor 页面文件。</span><span class="sxs-lookup"><span data-stu-id="17a72-147">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="17a72-148">URL 未包含页面时，`Index` 为默认页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-148">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="17a72-149">编写基本窗体</span><span class="sxs-lookup"><span data-stu-id="17a72-149">Write a basic form</span></span>

<span data-ttu-id="17a72-150">由于 Razor 页面的设计，在构建应用时可轻松实施用于 Web 浏览器的常用模式。</span><span class="sxs-lookup"><span data-stu-id="17a72-150">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="17a72-151">[模型绑定](xref:mvc/models/model-binding)、[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 HTML 帮助程序均只可用于 Razor 页面类中定义的属性。</span><span class="sxs-lookup"><span data-stu-id="17a72-151">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="17a72-152">请参考为 `Contact` 模型实现基本的“联系我们”窗体的页面：</span><span class="sxs-lookup"><span data-stu-id="17a72-152">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="17a72-153">在本文档中的示例中，`DbContext` 在 [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 文件中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="17a72-153">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="17a72-154">数据模型：</span><span class="sxs-lookup"><span data-stu-id="17a72-154">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="17a72-155">数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="17a72-155">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="17a72-156">Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-156">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="17a72-157">Pages/Create.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="17a72-157">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="17a72-158">按照惯例，`PageModel` 类命名为 `<PageName>Model`并且它与页面位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="17a72-158">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="17a72-159">使用 `PageModel` 类，可以将页面的逻辑与其展示分离开来。</span><span class="sxs-lookup"><span data-stu-id="17a72-159">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="17a72-160">它定义了页面处理程序，用于处理发送到页面的请求和用于呈现页面的数据。</span><span class="sxs-lookup"><span data-stu-id="17a72-160">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="17a72-161">借助这种分离，可以通过[依赖关系注入](xref:fundamentals/dependency-injection)管理页面依赖关系，并对页面执行[单元测试](xref:test/razor-pages-tests)。</span><span class="sxs-lookup"><span data-stu-id="17a72-161">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="17a72-162">页面包含 `OnPostAsync` 处理程序方法，它在 `POST` 请求上运行（当用户发布窗体时）。</span><span class="sxs-lookup"><span data-stu-id="17a72-162">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="17a72-163">可以为任何 HTTP 谓词添加处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="17a72-163">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="17a72-164">最常见的处理程序是：</span><span class="sxs-lookup"><span data-stu-id="17a72-164">The most common handlers are:</span></span>

* <span data-ttu-id="17a72-165">`OnGet`，用于初始化页面所需的状态。</span><span class="sxs-lookup"><span data-stu-id="17a72-165">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="17a72-166">[OnGet](#OnGet) 示例。</span><span class="sxs-lookup"><span data-stu-id="17a72-166">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="17a72-167">`OnPost`，用于处理窗体提交。</span><span class="sxs-lookup"><span data-stu-id="17a72-167">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="17a72-168">`Async` 命名后缀为可选，但是按照惯例通常会将它用于异步函数。</span><span class="sxs-lookup"><span data-stu-id="17a72-168">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="17a72-169">前面示例中的 `OnPostAsync` 代码看上去与通常在控制器中编写的内容相似。</span><span class="sxs-lookup"><span data-stu-id="17a72-169">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="17a72-170">前面的代码通常用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-170">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="17a72-171">多数 MVC 基元（例如[模型绑定](xref:mvc/models/model-binding)、[验证](xref:mvc/models/validation)和操作结果）都是共享的。</span><span class="sxs-lookup"><span data-stu-id="17a72-171">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="17a72-172">之前的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="17a72-172">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="17a72-173">`OnPostAsync` 的基本流：</span><span class="sxs-lookup"><span data-stu-id="17a72-173">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="17a72-174">检查验证错误。</span><span class="sxs-lookup"><span data-stu-id="17a72-174">Check for validation errors.</span></span>

* <span data-ttu-id="17a72-175">如果没有错误，则保存数据并重定向。</span><span class="sxs-lookup"><span data-stu-id="17a72-175">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="17a72-176">如果有错误，则再次显示页面并附带验证消息。</span><span class="sxs-lookup"><span data-stu-id="17a72-176">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="17a72-177">客户端验证与传统的 ASP.NET Core MVC 应用程序相同。</span><span class="sxs-lookup"><span data-stu-id="17a72-177">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="17a72-178">很多情况下，都会在客户端上检测到验证错误，并且从不将它们提交到服务器。</span><span class="sxs-lookup"><span data-stu-id="17a72-178">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="17a72-179">成功输入数据后，`OnPostAsync` 处理程序方法调用 `RedirectToPage` 帮助程序方法来返回 `RedirectToPageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="17a72-179">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="17a72-180">`RedirectToPage` 是新的操作结果，类似于 `RedirectToAction` 或 `RedirectToRoute`，但是已针对页面进行自定义。</span><span class="sxs-lookup"><span data-stu-id="17a72-180">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="17a72-181">在前面的示例中，它将重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="17a72-181">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="17a72-182">[页面 URL 生成](#url_gen)部分中详细介绍了 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="17a72-182">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="17a72-183">提交的窗体存在（已传递到服务器的）验证错误时，`OnPostAsync` 处理程序方法调用 `Page` 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="17a72-183">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="17a72-184">`Page` 返回 `PageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="17a72-184">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="17a72-185">返回 `Page` 的过程与控制器中的操作返回 `View` 的过程相似。</span><span class="sxs-lookup"><span data-stu-id="17a72-185">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="17a72-186">`PageResult` 是处理程序方法的</span><span class="sxs-lookup"><span data-stu-id="17a72-186">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="17a72-187">默认返回类型。</span><span class="sxs-lookup"><span data-stu-id="17a72-187">return type for a handler method.</span></span> <span data-ttu-id="17a72-188">返回 `void` 的处理程序方法将显示页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-188">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="17a72-189">`Customer` 属性使用 `[BindProperty]` 特性来选择加入模型绑定。</span><span class="sxs-lookup"><span data-stu-id="17a72-189">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="17a72-190">默认情况下，Razor 页面只绑定带有非 GET 谓词的属性。</span><span class="sxs-lookup"><span data-stu-id="17a72-190">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="17a72-191">绑定属性可以减少需要编写的代码量。</span><span class="sxs-lookup"><span data-stu-id="17a72-191">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="17a72-192">绑定通过使用相同的属性显示窗体字段 (`<input asp-for="Customer.Name">`) 来减少代码，并接受输入。</span><span class="sxs-lookup"><span data-stu-id="17a72-192">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="17a72-193">主页 (Index.cshtml)：</span><span class="sxs-lookup"><span data-stu-id="17a72-193">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="17a72-194">关联的 `PageModel` 类 (Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="17a72-194">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="17a72-195">Index.cshtml 文件包含以下标记来创建每个联系人项的编辑链接：</span><span class="sxs-lookup"><span data-stu-id="17a72-195">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="17a72-196">[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 使用 `asp-route-{value}` 属性生成“编辑”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="17a72-196">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="17a72-197">此链接包含路由数据及联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="17a72-197">The link contains route data with the contact ID.</span></span> <span data-ttu-id="17a72-198">例如 `http://localhost:5000/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="17a72-198">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="17a72-199">使用 `asp-area` 属性指定区域。</span><span class="sxs-lookup"><span data-stu-id="17a72-199">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="17a72-200">有关更多信息，请参见<xref:mvc/controllers/areas>。</span><span class="sxs-lookup"><span data-stu-id="17a72-200">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="17a72-201">Pages/Edit.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-201">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="17a72-202">第一行包含 `@page "{id:int}"` 指令。</span><span class="sxs-lookup"><span data-stu-id="17a72-202">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="17a72-203">路由约束 `"{id:int}"` 告诉页面接受包含 `int` 路由数据的页面请求。</span><span class="sxs-lookup"><span data-stu-id="17a72-203">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="17a72-204">如果页面请求未包含可转换为 `int` 的路由数据，则运行时返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="17a72-204">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="17a72-205">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="17a72-205">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="17a72-206">Pages/Edit.cshtml.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-206">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="17a72-207">Index.cshtml 文件还包含用于为每个客户联系人创建删除按钮的标记：</span><span class="sxs-lookup"><span data-stu-id="17a72-207">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="17a72-208">删除按钮采用 HTML 呈现，其 `formaction` 包括参数：</span><span class="sxs-lookup"><span data-stu-id="17a72-208">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="17a72-209">`asp-route-id` 属性指定的客户联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="17a72-209">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="17a72-210">`asp-page-handler` 属性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="17a72-210">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="17a72-211">下面是呈现的删除按钮的示例，其中客户联系人 ID 为 `1`：</span><span class="sxs-lookup"><span data-stu-id="17a72-211">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="17a72-212">选中按钮时，向服务器发送窗体 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="17a72-212">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="17a72-213">按照惯例，根据方案 `OnPost[handler]Async` 基于 `handler` 参数的值来选择处理程序方法的名称。</span><span class="sxs-lookup"><span data-stu-id="17a72-213">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="17a72-214">因为本示例中 `handler` 是 `delete`，因此 `OnPostDeleteAsync` 处理程序方法用于处理 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="17a72-214">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="17a72-215">如果 `asp-page-handler` 设置为不同值（如 `remove`），则选择名称为 `OnPostRemoveAsync` 的页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="17a72-215">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="17a72-216">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="17a72-216">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="17a72-217">接受来自查询字符串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="17a72-217">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="17a72-218">使用 `FindAsync` 查询客户联系人的数据库。</span><span class="sxs-lookup"><span data-stu-id="17a72-218">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="17a72-219">如果找到客户联系人，则从客户联系人列表将其删除。</span><span class="sxs-lookup"><span data-stu-id="17a72-219">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="17a72-220">数据库将更新。</span><span class="sxs-lookup"><span data-stu-id="17a72-220">The database is updated.</span></span>
* <span data-ttu-id="17a72-221">调用 `RedirectToPage`，重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="17a72-221">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="17a72-222">按需标记页面属性</span><span class="sxs-lookup"><span data-stu-id="17a72-222">Mark page properties as required</span></span>

<span data-ttu-id="17a72-223">`PageModel` 上的属性可通过 [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) 特性进行修饰：</span><span class="sxs-lookup"><span data-stu-id="17a72-223">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="17a72-224">有关详细信息，请参阅[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="17a72-224">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="17a72-225">使用 OnGet 处理程序管理 HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="17a72-225">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="17a72-226">HEAD 请求可以检索特定资源的标头。</span><span class="sxs-lookup"><span data-stu-id="17a72-226">HEAD requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="17a72-227">与 GET 请求不同，HEAD 请求不返回响应正文。</span><span class="sxs-lookup"><span data-stu-id="17a72-227">Unlike GET requests, HEAD requests don't return a response body.</span></span>

<span data-ttu-id="17a72-228">通常，针对 HEAD 请求创建和调用 HEAD 处理程序：</span><span class="sxs-lookup"><span data-stu-id="17a72-228">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="17a72-229">如果未定义 HEAD 处理程序 (`OnHead`)，Razor 页面会回退以调用 ASP.NET Core 2.1 或更高版本中的 GET 页处理程序 (`OnGet`)。</span><span class="sxs-lookup"><span data-stu-id="17a72-229">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="17a72-230">在 ASP.NET Core 2.1 和 2.2 中，`Startup.Configure` 中的 [SetCompatibilityVersion](xref:mvc/compatibility-version) 会发生此行为：</span><span class="sxs-lookup"><span data-stu-id="17a72-230">In ASP.NET Core 2.1 and 2.2, this behavior occurs with the [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.Configure`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="17a72-231">默认模板在 ASP.NET Core 2.1 和 2.2 中生成 `SetCompatibilityVersion` 调用。</span><span class="sxs-lookup"><span data-stu-id="17a72-231">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span>

<span data-ttu-id="17a72-232">`SetCompatibilityVersion` 有效地将 Razor 页面选项 `AllowMappingHeadRequestsToGetHandler` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="17a72-232">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="17a72-233">可以显式地选择使用特定行为，而不是通过 `SetCompatibilityVersion` 选择使用所有 2.1 行为。</span><span class="sxs-lookup"><span data-stu-id="17a72-233">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="17a72-234">以下代码选择使用将 HEAD 映射到 GET 处理程序这一行为。</span><span class="sxs-lookup"><span data-stu-id="17a72-234">The following code opts into the mapping HEAD requests to the GET handler.</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="17a72-235">XSRF/CSRF 和 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="17a72-235">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="17a72-236">无需为[防伪验证](xref:security/anti-request-forgery)编写任何代码。</span><span class="sxs-lookup"><span data-stu-id="17a72-236">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="17a72-237">Razor 页面自动将防伪标记生成过程和验证过程包含在内。</span><span class="sxs-lookup"><span data-stu-id="17a72-237">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="17a72-238">将布局、分区、模板和标记帮助程序用于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="17a72-238">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="17a72-239">页面可使用 Razor 视图引擎的所有功能。</span><span class="sxs-lookup"><span data-stu-id="17a72-239">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="17a72-240">布局、分区、模板、标记帮助程序、_ViewStart.cshtml 和 _ViewImports.cshtml 的工作方式与它们在传统的 Razor 视图中的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="17a72-240">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="17a72-241">让我们使用其中的一些功能来整理此页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-241">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="17a72-242">向 Pages/Shared/_Layout.cshtml 添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="17a72-242">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="17a72-243">向 Pages/_Layout.cshtml 添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="17a72-243">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="17a72-244">[布局](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="17a72-244">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="17a72-245">控制每个页面的布局（页面选择退出布局时除外）。</span><span class="sxs-lookup"><span data-stu-id="17a72-245">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="17a72-246">导入 HTML 结构，例如 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="17a72-246">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="17a72-247">请参阅[布局页面](xref:mvc/views/layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="17a72-247">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="17a72-248">在 Pages/_ViewStart.cshtml 中设置 [Layout](xref:mvc/views/layout#specifying-a-layout) 属性：</span><span class="sxs-lookup"><span data-stu-id="17a72-248">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="17a72-249">布局位于“页面/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="17a72-249">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="17a72-250">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="17a72-250">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="17a72-251">可以从“页面/共享”文件夹下的任意 Razor 页面使用“页面”文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="17a72-251">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="17a72-252">布局文件应位于 Pages/Shared 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="17a72-252">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="17a72-253">布局位于“页面”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="17a72-253">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="17a72-254">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="17a72-254">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="17a72-255">可以从“页面”文件夹下的任意 Razor 页面使用“页面”文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="17a72-255">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="17a72-256">建议不要将布局文件放在“视图/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="17a72-256">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="17a72-257">视图/共享 是一种 MVC 视图模式。</span><span class="sxs-lookup"><span data-stu-id="17a72-257">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="17a72-258">Razor 页面旨在依赖文件夹层次结构，而非路径约定。</span><span class="sxs-lookup"><span data-stu-id="17a72-258">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="17a72-259">Razor 页面中的视图搜索包含“页面”文件夹。</span><span class="sxs-lookup"><span data-stu-id="17a72-259">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="17a72-260">用于 MVC 控制器和传统 Razor 视图的布局、模板和分区可直接工作。</span><span class="sxs-lookup"><span data-stu-id="17a72-260">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="17a72-261">添加 Pages/_ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-261">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="17a72-262">本教程的后续部分中将介绍 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="17a72-262">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="17a72-263">`@addTagHelper` 指令将[内置标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/Index)引入“页面”文件夹中的所有页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-263">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="17a72-264">页面上显式使用 `@namespace` 指令后：</span><span class="sxs-lookup"><span data-stu-id="17a72-264">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="17a72-265">此指令将为页面设置命名空间。</span><span class="sxs-lookup"><span data-stu-id="17a72-265">The directive sets the namespace for the page.</span></span> <span data-ttu-id="17a72-266">`@model` 指令无需包含命名空间。</span><span class="sxs-lookup"><span data-stu-id="17a72-266">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="17a72-267">_ViewImports.cshtml 中包含 `@namespace` 指令后，指定的命名空间将为在导入 `@namespace` 指令的页面中生成的命名空间提供前缀。</span><span class="sxs-lookup"><span data-stu-id="17a72-267">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="17a72-268">生成的命名空间的剩余部分（后缀部分）是包含 _ViewImports.cshtml 的文件夹与包含页面的文件夹之间以点分隔的相对路径。</span><span class="sxs-lookup"><span data-stu-id="17a72-268">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="17a72-269">例如，`PageModel` 类 Pages/Customers/Edit.cshtml.cs 显式设置命名空间：</span><span class="sxs-lookup"><span data-stu-id="17a72-269">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="17a72-270">Pages/_ViewImports.cshtml 文件设置以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="17a72-270">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="17a72-271">为 Pages/Customers/Edit.cshtml Razor 页面生成的命名空间与 `PageModel` 类相同。</span><span class="sxs-lookup"><span data-stu-id="17a72-271">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="17a72-272">`@namespace` 也可用于传统的 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="17a72-272">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="17a72-273">原始的 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-273">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="17a72-274">更新后的 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="17a72-274">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="17a72-275">[Razor 页面初学者项目](#rpvs17)包含 Pages/_ValidationScriptsPartial.cshtml，它与客户端验证联合。</span><span class="sxs-lookup"><span data-stu-id="17a72-275">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="17a72-276">有关分部视图的详细信息，请参阅 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="17a72-276">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="17a72-277">页面的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="17a72-277">URL generation for Pages</span></span>

<span data-ttu-id="17a72-278">之前显示的 `Create` 页面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="17a72-278">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="17a72-279">应用具有以下文件/文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="17a72-279">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="17a72-280">/Pages</span><span class="sxs-lookup"><span data-stu-id="17a72-280">*/Pages*</span></span>

  * <span data-ttu-id="17a72-281">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="17a72-281">*Index.cshtml*</span></span>
  * <span data-ttu-id="17a72-282">/Customers</span><span class="sxs-lookup"><span data-stu-id="17a72-282">*/Customers*</span></span>

    * <span data-ttu-id="17a72-283">Create.cshtml</span><span class="sxs-lookup"><span data-stu-id="17a72-283">*Create.cshtml*</span></span>
    * <span data-ttu-id="17a72-284">Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="17a72-284">*Edit.cshtml*</span></span>
    * <span data-ttu-id="17a72-285">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="17a72-285">*Index.cshtml*</span></span>

<span data-ttu-id="17a72-286">成功后，Pages/Customers/Create.cshtml 和 Pages/Customers/Edit.cshtml 页面将重定向到 Pages/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="17a72-286">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="17a72-287">字符串 `/Index` 是用于访问上一页的 URI 的组成部分。</span><span class="sxs-lookup"><span data-stu-id="17a72-287">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="17a72-288">可以使用字符串 `/Index` 生成 Pages/Index.cshtml 页面的 URI。</span><span class="sxs-lookup"><span data-stu-id="17a72-288">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="17a72-289">例如:</span><span class="sxs-lookup"><span data-stu-id="17a72-289">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="17a72-290">页面名称是从根“/Pages”文件夹到页面的路径（包含前导 `/`，例如 `/Index`）。</span><span class="sxs-lookup"><span data-stu-id="17a72-290">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="17a72-291">与硬编码 URL 相比，前面的 URL 生成示例提供了改进的选项和功能。</span><span class="sxs-lookup"><span data-stu-id="17a72-291">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="17a72-292">URL 生成使用[路由](xref:mvc/controllers/routing)，并且可以根据目标路径定义路由的方式生成参数并对参数编码。</span><span class="sxs-lookup"><span data-stu-id="17a72-292">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="17a72-293">页面的 URL 生成支持相对名称。</span><span class="sxs-lookup"><span data-stu-id="17a72-293">URL generation for pages supports relative names.</span></span> <span data-ttu-id="17a72-294">下表显示了 Pages/Customers/Create.cshtml 中不同的 `RedirectToPage` 参数选择的索引页：</span><span class="sxs-lookup"><span data-stu-id="17a72-294">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="17a72-295">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="17a72-295">RedirectToPage(x)</span></span>| <span data-ttu-id="17a72-296">页面</span><span class="sxs-lookup"><span data-stu-id="17a72-296">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="17a72-297">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="17a72-297">RedirectToPage("/Index")</span></span> | <span data-ttu-id="17a72-298">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="17a72-298">*Pages/Index*</span></span> |
| <span data-ttu-id="17a72-299">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="17a72-299">RedirectToPage("./Index");</span></span> | <span data-ttu-id="17a72-300">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="17a72-300">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="17a72-301">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="17a72-301">RedirectToPage("../Index")</span></span> | <span data-ttu-id="17a72-302">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="17a72-302">*Pages/Index*</span></span> |
| <span data-ttu-id="17a72-303">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="17a72-303">RedirectToPage("Index")</span></span>  | <span data-ttu-id="17a72-304">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="17a72-304">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="17a72-305">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是相对名称。</span><span class="sxs-lookup"><span data-stu-id="17a72-305">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="17a72-306">结合 `RedirectToPage` 参数与当前页的路径来计算目标页面的名称。</span><span class="sxs-lookup"><span data-stu-id="17a72-306">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="17a72-307">构建结构复杂的站点时，相对名称链接很有用。</span><span class="sxs-lookup"><span data-stu-id="17a72-307">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="17a72-308">如果使用相对名称链接文件夹中的页面，则可以重命名该文件夹。</span><span class="sxs-lookup"><span data-stu-id="17a72-308">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="17a72-309">所有链接仍然有效（因为这些链接未包含此文件夹名称）。</span><span class="sxs-lookup"><span data-stu-id="17a72-309">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="17a72-310">若要重定向到不同[区域](xref:mvc/controllers/areas)中的页面，请指定区域：</span><span class="sxs-lookup"><span data-stu-id="17a72-310">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="17a72-311">有关更多信息，请参见<xref:mvc/controllers/areas>。</span><span class="sxs-lookup"><span data-stu-id="17a72-311">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="17a72-312">ViewData 特性</span><span class="sxs-lookup"><span data-stu-id="17a72-312">ViewData attribute</span></span>

<span data-ttu-id="17a72-313">可以通过 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) 将数据传递到页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-313">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="17a72-314">控制器或 Razor 页面模型上使用 `[ViewData]` 修饰的属性将其值存储在 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 中并从此处进行加载。</span><span class="sxs-lookup"><span data-stu-id="17a72-314">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="17a72-315">在下面的示例中，`AboutModel` 包含使用 `[ViewData]` 修饰的 `Title` 属性。</span><span class="sxs-lookup"><span data-stu-id="17a72-315">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="17a72-316">`Title` 属性设置为“关于”页面的标题：</span><span class="sxs-lookup"><span data-stu-id="17a72-316">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="17a72-317">在“关于”页面中，以模型属性的形式访问 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="17a72-317">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="17a72-318">在布局中，从 ViewData 字典读取标题：</span><span class="sxs-lookup"><span data-stu-id="17a72-318">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="17a72-319">TempData</span><span class="sxs-lookup"><span data-stu-id="17a72-319">TempData</span></span>

<span data-ttu-id="17a72-320">ASP.NET 在[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)上公开了 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) 属性。</span><span class="sxs-lookup"><span data-stu-id="17a72-320">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="17a72-321">此属性存储未读取的数据。</span><span class="sxs-lookup"><span data-stu-id="17a72-321">This property stores data until it's read.</span></span> <span data-ttu-id="17a72-322">`Keep` 和 `Peek` 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="17a72-322">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="17a72-323">多个请求需要数据时，`TempData` 有助于进行重定向。</span><span class="sxs-lookup"><span data-stu-id="17a72-323">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="17a72-324">`[TempData]` 是 ASP.NET Core 2.0 中的新属性，在控制器和页面上受支持。</span><span class="sxs-lookup"><span data-stu-id="17a72-324">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="17a72-325">下面的代码使用 `TempData` 设置 `Message` 的值：</span><span class="sxs-lookup"><span data-stu-id="17a72-325">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="17a72-326">Pages/Customers/Index.cshtml 文件中的以下标记使用 `TempData` 显示 `Message` 的值。</span><span class="sxs-lookup"><span data-stu-id="17a72-326">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="17a72-327">Pages/Customers/Index.cshtml.cs 页面模型将 `[TempData]` 属性应用到 `Message` 属性。</span><span class="sxs-lookup"><span data-stu-id="17a72-327">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="17a72-328">有关详细信息，请参阅 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="17a72-328">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="17a72-329">针对一个页面的多个处理程序</span><span class="sxs-lookup"><span data-stu-id="17a72-329">Multiple handlers per page</span></span>

<span data-ttu-id="17a72-330">以下页面使用 `asp-page-handler` 标记帮助程序为两个页面处理程序生成标记：</span><span class="sxs-lookup"><span data-stu-id="17a72-330">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="17a72-331">前面示例中的窗体包含两个提交按钮，每个提交按钮均使用 `FormActionTagHelper` 提交到不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="17a72-331">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="17a72-332">`asp-page-handler` 是 `asp-page` 的配套属性。</span><span class="sxs-lookup"><span data-stu-id="17a72-332">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="17a72-333">`asp-page-handler` 生成提交到页面定义的各个处理程序方法的 URL。</span><span class="sxs-lookup"><span data-stu-id="17a72-333">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="17a72-334">未指定 `asp-page`，因为示例已链接到当前页面。</span><span class="sxs-lookup"><span data-stu-id="17a72-334">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="17a72-335">页面模型：</span><span class="sxs-lookup"><span data-stu-id="17a72-335">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="17a72-336">前面的代码使用已命名处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="17a72-336">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="17a72-337">已命名处理程序方法通过采用名称中 `On<HTTP Verb>` 之后及 `Async` 之前的文本（如果有）创建。</span><span class="sxs-lookup"><span data-stu-id="17a72-337">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="17a72-338">在前面的示例中，页面方法是 OnPost**JoinList**Async 和 OnPost**JoinListUC**Async。</span><span class="sxs-lookup"><span data-stu-id="17a72-338">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="17a72-339">删除 OnPost 和 Async 后，处理程序名称为 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="17a72-339">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="17a72-340">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `http://localhost:5000/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="17a72-340">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="17a72-341">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="17a72-341">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="17a72-342">自定义路由</span><span class="sxs-lookup"><span data-stu-id="17a72-342">Custom routes</span></span>

<span data-ttu-id="17a72-343">使用 `@page` 指令，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="17a72-343">Use the `@page` directive to:</span></span>

* <span data-ttu-id="17a72-344">指定页面的自定义路由。</span><span class="sxs-lookup"><span data-stu-id="17a72-344">Specify a custom route to a page.</span></span> <span data-ttu-id="17a72-345">例如，使用 `@page "/Some/Other/Path"` 将“关于”页面的路由设置为 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="17a72-345">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="17a72-346">将段追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="17a72-346">Append segments to a page's default route.</span></span> <span data-ttu-id="17a72-347">例如，可使用 `@page "item"` 将“item”段添加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="17a72-347">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="17a72-348">将参数追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="17a72-348">Append parameters to a page's default route.</span></span> <span data-ttu-id="17a72-349">例如，`@page "{id}"` 页面需要 ID 参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="17a72-349">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="17a72-350">支持开头处以波形符 (`~`) 指定的相对于根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="17a72-350">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="17a72-351">例如，`@page "~/Some/Other/Path"` 和 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="17a72-351">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="17a72-352">可以通过指定路由模板 `@page "{handler?}"`，将 URL 中的查询字符串 `?handler=JoinList` 更改为路由段 `/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="17a72-352">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="17a72-353">如果你不喜欢 URL 中的查询字符串 `?handler=JoinList`，可以更改路由，将处理程序名称放在 URL 的路径部分。</span><span class="sxs-lookup"><span data-stu-id="17a72-353">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="17a72-354">可以通过在 `@page` 指令后面添加使用双引号括起来的路由模板来自定义路由。</span><span class="sxs-lookup"><span data-stu-id="17a72-354">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="17a72-355">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `http://localhost:5000/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="17a72-355">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="17a72-356">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `http://localhost:5000/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="17a72-356">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="17a72-357">`handler` 前面的 `?` 表示路由参数为可选。</span><span class="sxs-lookup"><span data-stu-id="17a72-357">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="17a72-358">配置和设置</span><span class="sxs-lookup"><span data-stu-id="17a72-358">Configuration and settings</span></span>

<span data-ttu-id="17a72-359">若要配置高级选项，请在 MVC 生成器上使用 `AddRazorPagesOptions` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="17a72-359">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="17a72-360">目前，可以使用 `RazorPagesOptions` 设置页面的根目录，或者为页面添加应用程序模型约定。</span><span class="sxs-lookup"><span data-stu-id="17a72-360">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="17a72-361">通过这种方式，我们在将来会实现更多扩展功能。</span><span class="sxs-lookup"><span data-stu-id="17a72-361">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="17a72-362">若要预编译视图，请参阅 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="17a72-362">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="17a72-363">[下载或查看示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="17a72-363">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="17a72-364">请参阅 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)，这篇文章以本文为基础编写。</span><span class="sxs-lookup"><span data-stu-id="17a72-364">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="17a72-365">指定 Razor 页面位于内容根目录中</span><span class="sxs-lookup"><span data-stu-id="17a72-365">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="17a72-366">默认情况下，Razor 页面位于 /Pages 目录的根位置。</span><span class="sxs-lookup"><span data-stu-id="17a72-366">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="17a72-367">向 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 添加 [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot)，以指定 Razor 页面位于应用的内容根目录 ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) 中：</span><span class="sxs-lookup"><span data-stu-id="17a72-367">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="17a72-368">指定 Razor 页面位于自定义根目录中</span><span class="sxs-lookup"><span data-stu-id="17a72-368">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="17a72-369">向 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 添加 [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot)，以指定 Razor 页面位于应用中自定义根目录位置（提供相对路径）：</span><span class="sxs-lookup"><span data-stu-id="17a72-369">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="17a72-370">其他资源</span><span class="sxs-lookup"><span data-stu-id="17a72-370">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
