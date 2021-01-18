---
title: ASP.NET Core 中的 Razor Pages 介绍
author: Rick-Anderson
description: 介绍了 ASP.NET Core 中的 Razor Pages 如何让编码以页面为中心的方案比使用 MVC 更容易和更高效。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: f8cdbbffae9b291923a6d425fef5526b0ec88f61
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253184"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="406cb-103">ASP.NET Core 中的 Razor Pages 介绍</span><span class="sxs-lookup"><span data-stu-id="406cb-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="406cb-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="406cb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="406cb-105">通过 Razor Pages 对基于页面的场景编码比使用控制器和视图更轻松、更高效。</span><span class="sxs-lookup"><span data-stu-id="406cb-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="406cb-106">若要查找使用模型视图控制器方法的教程，请参阅 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)。</span><span class="sxs-lookup"><span data-stu-id="406cb-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="406cb-107">本文档介绍 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="406cb-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="406cb-108">它并不是分步教程。</span><span class="sxs-lookup"><span data-stu-id="406cb-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="406cb-109">如果认为某些部分过于复杂，请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)。</span><span class="sxs-lookup"><span data-stu-id="406cb-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="406cb-110">有关 ASP.NET Core 的概述，请参阅 [ASP.NET Core 简介](xref:index)。</span><span class="sxs-lookup"><span data-stu-id="406cb-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="406cb-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="406cb-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="406cb-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cb-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="406cb-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cb-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="406cb-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="406cb-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="406cb-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cb-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="406cb-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cb-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="406cb-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="406cb-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="406cb-118">创建 Razor Pages 项目</span><span class="sxs-lookup"><span data-stu-id="406cb-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="406cb-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cb-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cb-120">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何创建 Razor Pages 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="406cb-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="406cb-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cb-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cb-122">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="406cb-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="406cb-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="406cb-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="406cb-124">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何创建 Razor Pages 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="406cb-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="406cb-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-125">Razor Pages</span></span>

<span data-ttu-id="406cb-126">Startup.cs 中已启用 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="406cb-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="406cb-127">请考虑一个基本页面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="406cb-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="406cb-128">前面的代码与具有控制器和视图的 ASP.NET Core 应用中使用的 [Razor 视图文件](xref:tutorials/first-mvc-app/adding-view)非常相似。</span><span class="sxs-lookup"><span data-stu-id="406cb-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="406cb-129">不同之处在于 [`@page`](xref:mvc/views/razor#page) 指令。</span><span class="sxs-lookup"><span data-stu-id="406cb-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="406cb-130">`@page` 使文件转换为一个 MVC 操作 ，这意味着它将直接处理请求，而无需通过控制器处理。</span><span class="sxs-lookup"><span data-stu-id="406cb-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="406cb-131">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="406cb-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="406cb-132">`@page` 会影响其他的 [Razor](xref:mvc/views/razor) 构造。</span><span class="sxs-lookup"><span data-stu-id="406cb-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="406cb-133">Razor Pages 文件名有 .cshtml 后缀。</span><span class="sxs-lookup"><span data-stu-id="406cb-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="406cb-134">将在以下两个文件中显示使用 `PageModel` 类的类似页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="406cb-135">Pages/Index2.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="406cb-136">Pages/Index2.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="406cb-137">按照惯例，`PageModel` 类文件的名称与追加 .cs 的 Razor Page 文件名称相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="406cb-138">例如，前面的 Razor Page 的名称为 Pages/Index2.cshtml。</span><span class="sxs-lookup"><span data-stu-id="406cb-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="406cb-139">包含 `PageModel` 类的文件的名称为 Pages/Index2.cshtml.cs。</span><span class="sxs-lookup"><span data-stu-id="406cb-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="406cb-140">页面的 URL 路径的关联由页面在文件系统中的位置决定。</span><span class="sxs-lookup"><span data-stu-id="406cb-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="406cb-141">下表显示了 Razor Page 路径及匹配的 URL：</span><span class="sxs-lookup"><span data-stu-id="406cb-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="406cb-142">文件名和路径</span><span class="sxs-lookup"><span data-stu-id="406cb-142">File name and path</span></span>               | <span data-ttu-id="406cb-143">匹配的 URL</span><span class="sxs-lookup"><span data-stu-id="406cb-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="406cb-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="406cb-145">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="406cb-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="406cb-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="406cb-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="406cb-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="406cb-149">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="406cb-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="406cb-150">注意：</span><span class="sxs-lookup"><span data-stu-id="406cb-150">Notes:</span></span>

* <span data-ttu-id="406cb-151">默认情况下，运行时在“Pages”文件夹中查找 Razor Pages 文件。</span><span class="sxs-lookup"><span data-stu-id="406cb-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="406cb-152">URL 未包含页面时，`Index` 为默认页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="406cb-153">编写基本窗体</span><span class="sxs-lookup"><span data-stu-id="406cb-153">Write a basic form</span></span>

<span data-ttu-id="406cb-154">由于 Razor Pages 的设计，在构建应用时可轻松实施用于 Web 浏览器的常用模式。</span><span class="sxs-lookup"><span data-stu-id="406cb-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="406cb-155">[模型绑定](xref:mvc/models/model-binding)、[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 HTML 帮助程序均只可用于 Razor Page 类中定义的属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="406cb-156">请参考为 `Contact` 模型实现的基本的“联系我们”窗体页面：</span><span class="sxs-lookup"><span data-stu-id="406cb-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="406cb-157">在本文档中的示例中，`DbContext` 在 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) 文件中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="406cb-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="406cb-158">内存中数据库需要 `Microsoft.EntityFrameworkCore.InMemory` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="406cb-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="406cb-159">数据模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="406cb-160">数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="406cb-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="406cb-161">Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="406cb-162">Pages/Create.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="406cb-163">按照惯例，`PageModel` 类命名为 `<PageName>Model`并且它与页面位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="406cb-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="406cb-164">使用 `PageModel` 类，可以将页面的逻辑与其展示分离开来。</span><span class="sxs-lookup"><span data-stu-id="406cb-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="406cb-165">它定义了页面处理程序，用于处理发送到页面的请求和用于呈现页面的数据。</span><span class="sxs-lookup"><span data-stu-id="406cb-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="406cb-166">这种隔离可实现：</span><span class="sxs-lookup"><span data-stu-id="406cb-166">This separation allows:</span></span>

* <span data-ttu-id="406cb-167">通过[依赖关系注入](xref:fundamentals/dependency-injection)管理页面依赖项。</span><span class="sxs-lookup"><span data-stu-id="406cb-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="406cb-168">单元测试</span><span class="sxs-lookup"><span data-stu-id="406cb-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="406cb-169">页面包含 `OnPostAsync` 处理程序方法，它在 `POST` 请求上运行（当用户发布窗体时）。</span><span class="sxs-lookup"><span data-stu-id="406cb-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="406cb-170">可以添加任何 HTTP 谓词的处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="406cb-171">最常见的处理程序是：</span><span class="sxs-lookup"><span data-stu-id="406cb-171">The most common handlers are:</span></span>

* <span data-ttu-id="406cb-172">`OnGet`，用于初始化页面所需的状态。</span><span class="sxs-lookup"><span data-stu-id="406cb-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="406cb-173">在上面的代码中，`OnGet` 方法显示 CreateModel.cshtml Razor Page。</span><span class="sxs-lookup"><span data-stu-id="406cb-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="406cb-174">`OnPost`，用于处理窗体提交。</span><span class="sxs-lookup"><span data-stu-id="406cb-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="406cb-175">`Async` 命名后缀为可选，但是按照惯例通常会将它用于异步函数。</span><span class="sxs-lookup"><span data-stu-id="406cb-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="406cb-176">前面的代码通常用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="406cb-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="406cb-177">如果你熟悉使用控制器和视图的 ASP.NET 应用：</span><span class="sxs-lookup"><span data-stu-id="406cb-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="406cb-178">前面示例中的 `OnPostAsync` 代码类似于典型的控制器代码。</span><span class="sxs-lookup"><span data-stu-id="406cb-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="406cb-179">大多数 MVC 基元（例如[模型绑定](xref:mvc/models/model-binding)、[验证](xref:mvc/models/validation)和操作结果）通过控制器和通过 Razor Pages 的运作方式相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="406cb-180">之前的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="406cb-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="406cb-181">`OnPostAsync` 的基本流：</span><span class="sxs-lookup"><span data-stu-id="406cb-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="406cb-182">检查验证错误。</span><span class="sxs-lookup"><span data-stu-id="406cb-182">Check for validation errors.</span></span>

* <span data-ttu-id="406cb-183">如果没有错误，则保存数据并重定向。</span><span class="sxs-lookup"><span data-stu-id="406cb-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="406cb-184">如果有错误，则再次显示页面并附带验证消息。</span><span class="sxs-lookup"><span data-stu-id="406cb-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="406cb-185">很多情况下，都会在客户端上检测到验证错误，并且从不将它们提交到服务器。</span><span class="sxs-lookup"><span data-stu-id="406cb-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="406cb-186">Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="406cb-187">Pages/Create.cshtml 中呈现的 HTML：</span><span class="sxs-lookup"><span data-stu-id="406cb-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="406cb-188">在前面的代码中，发布窗体：</span><span class="sxs-lookup"><span data-stu-id="406cb-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="406cb-189">对于有效数据：</span><span class="sxs-lookup"><span data-stu-id="406cb-189">With valid data:</span></span>

  * <span data-ttu-id="406cb-190">`OnPostAsync` 处理程序方法调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="406cb-191">`RedirectToPage` 返回 <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> 的实例。</span><span class="sxs-lookup"><span data-stu-id="406cb-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="406cb-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="406cb-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="406cb-193">是操作结果。</span><span class="sxs-lookup"><span data-stu-id="406cb-193">Is an action result.</span></span>
    * <span data-ttu-id="406cb-194">类似于 `RedirectToAction` 或 `RedirectToRoute`（用于控制器和视图）。</span><span class="sxs-lookup"><span data-stu-id="406cb-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="406cb-195">针对页面自定义。</span><span class="sxs-lookup"><span data-stu-id="406cb-195">Is customized for pages.</span></span> <span data-ttu-id="406cb-196">在前面的示例中，它将重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="406cb-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="406cb-197">[页面 URL 生成](#url_gen)部分中详细介绍了 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="406cb-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="406cb-198">对于传递给服务器的验证错误：</span><span class="sxs-lookup"><span data-stu-id="406cb-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="406cb-199">`OnPostAsync` 处理程序方法调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="406cb-200">`Page` 返回 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 的实例。</span><span class="sxs-lookup"><span data-stu-id="406cb-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="406cb-201">返回 `Page` 的过程与控制器中的操作返回 `View` 的过程相似。</span><span class="sxs-lookup"><span data-stu-id="406cb-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="406cb-202">`PageResult` 是处理程序方法的默认返回类型。</span><span class="sxs-lookup"><span data-stu-id="406cb-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="406cb-203">返回 `void` 的处理程序方法将显示页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="406cb-204">在前面的示例中，在 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 中的值结果不返回 false 的情况下发布窗体。</span><span class="sxs-lookup"><span data-stu-id="406cb-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="406cb-205">在此示例中，客户端上不显示任何验证错误。</span><span class="sxs-lookup"><span data-stu-id="406cb-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="406cb-206">本文档的后面将介绍验证错误处理。</span><span class="sxs-lookup"><span data-stu-id="406cb-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="406cb-207">对于客户端验证检测到的验证错误：</span><span class="sxs-lookup"><span data-stu-id="406cb-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="406cb-208">数据不会发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="406cb-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="406cb-209">本文档的后面将介绍客户端验证。</span><span class="sxs-lookup"><span data-stu-id="406cb-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="406cb-210">`Customer` 属性使用 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 特性来选择加入模型绑定：</span><span class="sxs-lookup"><span data-stu-id="406cb-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="406cb-211">`[BindProperty]` 不应 用于包含不应由客户端更改的属性的模型。</span><span class="sxs-lookup"><span data-stu-id="406cb-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="406cb-212">有关详细信息，请参阅[过度发布](xref:data/ef-rp/crud#overposting)。</span><span class="sxs-lookup"><span data-stu-id="406cb-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="406cb-213">Razor Pages 只绑定带有非 `GET` 谓词的属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="406cb-214">如果绑定到属性，则无需通过编写代码将 HTTP 数据转换为模型类型。</span><span class="sxs-lookup"><span data-stu-id="406cb-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="406cb-215">绑定通过使用相同的属性显示窗体字段 (`<input asp-for="Customer.Name">`) 来减少代码，并接受输入。</span><span class="sxs-lookup"><span data-stu-id="406cb-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="406cb-216">查看 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="406cb-217">在前面的代码中，[输入标记帮助程序](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` 将 HTML `<input>` 元素绑定到 `Customer.Name` 模型表达式。</span><span class="sxs-lookup"><span data-stu-id="406cb-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="406cb-218">使用 [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) 提供标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="406cb-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="406cb-219">主页</span><span class="sxs-lookup"><span data-stu-id="406cb-219">The home page</span></span>

<span data-ttu-id="406cb-220">Index.cshtml 是主页：</span><span class="sxs-lookup"><span data-stu-id="406cb-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="406cb-221">关联的 `PageModel` 类 (Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="406cb-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="406cb-222">Index.cshtml 文件包含以下标记：</span><span class="sxs-lookup"><span data-stu-id="406cb-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="406cb-223">`<a /a>` [定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)使用 `asp-route-{value}` 属性生成“编辑”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="406cb-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="406cb-224">此链接包含路由数据及联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="406cb-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="406cb-225">例如 `https://localhost:5001/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="406cb-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="406cb-226">[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="406cb-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="406cb-227">Index.cshtml 文件包含用于为每个客户联系人创建删除按钮的标记：</span><span class="sxs-lookup"><span data-stu-id="406cb-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="406cb-228">呈现的 HTML：</span><span class="sxs-lookup"><span data-stu-id="406cb-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="406cb-229">删除按钮采用 HTML 呈现，其 [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) 包括参数：</span><span class="sxs-lookup"><span data-stu-id="406cb-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="406cb-230">`asp-route-id` 属性指定的客户联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="406cb-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="406cb-231">`asp-page-handler` 属性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="406cb-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="406cb-232">选中按钮时，向服务器发送窗体 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="406cb-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="406cb-233">按照惯例，根据方案 `OnPost[handler]Async` 基于 `handler` 参数的值来选择处理程序方法的名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="406cb-234">因为本示例中 `handler` 是 `delete`，因此 `OnPostDeleteAsync` 处理程序方法用于处理 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="406cb-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="406cb-235">如果 `asp-page-handler` 设置为其他值（如 `remove`），则选择名称为 `OnPostRemoveAsync` 的处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="406cb-236">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="406cb-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="406cb-237">获取来自查询字符串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="406cb-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="406cb-238">使用 `FindAsync` 查询客户联系人的数据库。</span><span class="sxs-lookup"><span data-stu-id="406cb-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="406cb-239">如果找到客户联系人，则会将其删除，并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="406cb-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="406cb-240">调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>，重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="406cb-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="406cb-241">Edit.cshtml 文件</span><span class="sxs-lookup"><span data-stu-id="406cb-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="406cb-242">第一行包含 `@page "{id:int}"` 指令。</span><span class="sxs-lookup"><span data-stu-id="406cb-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="406cb-243">路由约束 `"{id:int}"` 告诉页面接受包含 `int` 路由数据的页面请求。</span><span class="sxs-lookup"><span data-stu-id="406cb-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="406cb-244">如果页面请求未包含可转换为 `int` 的路由数据，则运行时返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="406cb-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="406cb-245">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="406cb-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="406cb-246">Edit.cshtml.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="406cb-247">验证</span><span class="sxs-lookup"><span data-stu-id="406cb-247">Validation</span></span>

<span data-ttu-id="406cb-248">验证规则：</span><span class="sxs-lookup"><span data-stu-id="406cb-248">Validation rules:</span></span>

* <span data-ttu-id="406cb-249">在模型类中以声明方式指定。</span><span class="sxs-lookup"><span data-stu-id="406cb-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="406cb-250">在应用中的所有位置强制执行。</span><span class="sxs-lookup"><span data-stu-id="406cb-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="406cb-251"><xref:System.ComponentModel.DataAnnotations> 命名空间提供一组内置验证特性，可通过声明方式应用于类或属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="406cb-252">DataAnnotations 还包含 [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 等格式特性，有助于格式设置但不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="406cb-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="406cb-253">请考虑 `Customer` 模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="406cb-254">使用以下 Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="406cb-255">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="406cb-255">The preceding code:</span></span>

* <span data-ttu-id="406cb-256">包括 jQuery 和 jQuery 验证脚本。</span><span class="sxs-lookup"><span data-stu-id="406cb-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="406cb-257">使用 `<div />` 和 `<span />` [标记帮助程序](xref:mvc/views/tag-helpers/intro)以实现：</span><span class="sxs-lookup"><span data-stu-id="406cb-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="406cb-258">客户端验证。</span><span class="sxs-lookup"><span data-stu-id="406cb-258">Client-side validation.</span></span>
  * <span data-ttu-id="406cb-259">验证错误呈现。</span><span class="sxs-lookup"><span data-stu-id="406cb-259">Validation error rendering.</span></span>

* <span data-ttu-id="406cb-260">则会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="406cb-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="406cb-261">如果在不使用名称值的情况下发布“创建”窗体，则将显示错误消息“名称字段是必需的”。</span><span class="sxs-lookup"><span data-stu-id="406cb-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="406cb-262">窗体上。</span><span class="sxs-lookup"><span data-stu-id="406cb-262">on the form.</span></span> <span data-ttu-id="406cb-263">如果客户端上已启用 JavaScript，浏览器会显示错误，而不会发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="406cb-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="406cb-264">`[StringLength(10)]` 特性在呈现的 HTML 上生成 `data-val-length-max="10"`。</span><span class="sxs-lookup"><span data-stu-id="406cb-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="406cb-265">`data-val-length-max` 阻止浏览器输入超过指定最大长度的内容。</span><span class="sxs-lookup"><span data-stu-id="406cb-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="406cb-266">如果使用 [Fiddler](https://www.telerik.com/fiddler) 等工具来编辑和重播文章：</span><span class="sxs-lookup"><span data-stu-id="406cb-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="406cb-267">对于长度超过 10 的名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-267">With the name longer than 10.</span></span>
* <span data-ttu-id="406cb-268">错误消息“字段名称必须是最大长度为 10 的字符串。”</span><span class="sxs-lookup"><span data-stu-id="406cb-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="406cb-269">将返回。</span><span class="sxs-lookup"><span data-stu-id="406cb-269">is returned.</span></span>

<span data-ttu-id="406cb-270">考虑下列 `Movie` 模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="406cb-271">验证特性指定要对应用这些特性的模型属性强制执行的行为：</span><span class="sxs-lookup"><span data-stu-id="406cb-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="406cb-272">`Required` 和 `MinimumLength` 特性表示属性必须有值，但用户可输入空格来满足此验证。</span><span class="sxs-lookup"><span data-stu-id="406cb-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="406cb-273">`RegularExpression` 特性用于限制可输入的字符。</span><span class="sxs-lookup"><span data-stu-id="406cb-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="406cb-274">在上述代码中，即“Genre”（分类）：</span><span class="sxs-lookup"><span data-stu-id="406cb-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="406cb-275">只能使用字母。</span><span class="sxs-lookup"><span data-stu-id="406cb-275">Must only use letters.</span></span>
  * <span data-ttu-id="406cb-276">第一个字母必须为大写。</span><span class="sxs-lookup"><span data-stu-id="406cb-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="406cb-277">不允许使用空格、数字和特殊字符。</span><span class="sxs-lookup"><span data-stu-id="406cb-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="406cb-278">`RegularExpression`“Rating”（分级）：</span><span class="sxs-lookup"><span data-stu-id="406cb-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="406cb-279">要求第一个字符为大写字母。</span><span class="sxs-lookup"><span data-stu-id="406cb-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="406cb-280">允许在后续空格中使用特殊字符和数字。</span><span class="sxs-lookup"><span data-stu-id="406cb-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="406cb-281">“PG-13”对“分级”有效，但对于“分类”无效。</span><span class="sxs-lookup"><span data-stu-id="406cb-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="406cb-282">`Range` 特性将值限制在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="406cb-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="406cb-283">`StringLength` 特性可以设置字符串属性的最大长度，以及可选的最小长度。</span><span class="sxs-lookup"><span data-stu-id="406cb-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="406cb-284">从本质上来说，需要值类型（如 `decimal`、`int`、`float`、`DateTime`），但不需要 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="406cb-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="406cb-285">`Movie` 模型的“创建”页面显示无效值错误：</span><span class="sxs-lookup"><span data-stu-id="406cb-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![带有多个 jQuery 客户端验证错误的电影视图表单](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="406cb-287">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="406cb-287">For more information, see:</span></span>

* [<span data-ttu-id="406cb-288">将验证添加到电影应用</span><span class="sxs-lookup"><span data-stu-id="406cb-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="406cb-289">[ASP.NET Core 中的模型验证](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="406cb-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="406cb-290">使用 OnGet 处理程序回退来处理 HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="406cb-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="406cb-291">`HEAD` 请求可检索特定资源的标头。</span><span class="sxs-lookup"><span data-stu-id="406cb-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="406cb-292">与 `GET` 请求不同，`HEAD` 请求不返回响应正文。</span><span class="sxs-lookup"><span data-stu-id="406cb-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="406cb-293">通常，针对 `HEAD` 请求创建和调用 `OnHead` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="406cb-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="406cb-294">如果未定义 `OnHead` 处理程序，则 Razor Pages 会回退到调用 `OnGet` 处理程序。</span><span class="sxs-lookup"><span data-stu-id="406cb-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="406cb-295">XSRF/CSRF 和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="406cb-296">Razor Pages 由 [防伪造验证](xref:security/anti-request-forgery)保护。</span><span class="sxs-lookup"><span data-stu-id="406cb-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="406cb-297">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 将防伪造令牌注入 HTML 窗体元素。</span><span class="sxs-lookup"><span data-stu-id="406cb-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="406cb-298">将布局、分区、模板和标记帮助程序用于 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="406cb-299">页面可使用 Razor 视图引擎的所有功能。</span><span class="sxs-lookup"><span data-stu-id="406cb-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="406cb-300">布局、分区、模板、标记帮助程序、_ViewStart.cshtml 和 _ViewImports.cshtml 的工作方式与它们在传统的 Razor 视图中的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="406cb-301">让我们使用其中的一些功能来整理此页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="406cb-302">向 Pages/Shared/_Layout.cshtml 添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="406cb-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="406cb-303">[布局](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="406cb-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="406cb-304">控制每个页面的布局（页面选择退出布局时除外）。</span><span class="sxs-lookup"><span data-stu-id="406cb-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="406cb-305">导入 HTML 结构，例如 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="406cb-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="406cb-306">调用 `@RenderBody()` 时，呈现 Razor Page 的内容。</span><span class="sxs-lookup"><span data-stu-id="406cb-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="406cb-307">有关详细信息，请参阅[布局页面](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="406cb-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="406cb-308">在 Pages/_ViewStart.cshtml 中设置 [Layout](xref:mvc/views/layout#specifying-a-layout) 属性：</span><span class="sxs-lookup"><span data-stu-id="406cb-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="406cb-309">布局位于“页面/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="406cb-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="406cb-310">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="406cb-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="406cb-311">可以从“Pages”文件夹下的任意 Razor 页面使用“Pages/Shared”文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="406cb-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="406cb-312">布局文件应位于 Pages/Shared 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="406cb-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="406cb-313">建议不要将布局文件放在“视图/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="406cb-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="406cb-314">视图/共享 是一种 MVC 视图模式。</span><span class="sxs-lookup"><span data-stu-id="406cb-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="406cb-315">Razor Pages 旨在依赖文件夹层次结构，而非路径约定。</span><span class="sxs-lookup"><span data-stu-id="406cb-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="406cb-316">Razor Page 中的视图搜索包含“Pages”文件夹。</span><span class="sxs-lookup"><span data-stu-id="406cb-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="406cb-317">用于 MVC 控制器和传统 Razor 视图的布局、模板和分区可正常运行。</span><span class="sxs-lookup"><span data-stu-id="406cb-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="406cb-318">添加 Pages/_ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="406cb-319">本教程的后续部分中将介绍 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="406cb-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="406cb-320">`@addTagHelper` 指令将[内置标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/Index)引入“页面”文件夹中的所有页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="406cb-321">页面上设置的 `@namespace` 指令：</span><span class="sxs-lookup"><span data-stu-id="406cb-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="406cb-322">`@namespace` 指令将为页面设置命名空间。</span><span class="sxs-lookup"><span data-stu-id="406cb-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="406cb-323">`@model` 指令无需包含命名空间。</span><span class="sxs-lookup"><span data-stu-id="406cb-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="406cb-324">_ViewImports.cshtml 中包含 `@namespace` 指令后，指定的命名空间将为在导入 `@namespace` 指令的页面中生成的命名空间提供前缀。</span><span class="sxs-lookup"><span data-stu-id="406cb-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="406cb-325">生成的命名空间的剩余部分（后缀部分）是包含 _ViewImports.cshtml 的文件夹与包含页面的文件夹之间以点分隔的相对路径。</span><span class="sxs-lookup"><span data-stu-id="406cb-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="406cb-326">例如，`PageModel` 类 Pages/Customers/Edit.cshtml.cs 显式设置命名空间：</span><span class="sxs-lookup"><span data-stu-id="406cb-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="406cb-327">Pages/_ViewImports.cshtml 文件设置以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="406cb-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="406cb-328">为 Pages/Customers/Edit.cshtml Razor Page 生成的命名空间与 `PageModel` 类相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="406cb-329">`@namespace`  *也适用于传统 Razor 视图。*</span><span class="sxs-lookup"><span data-stu-id="406cb-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="406cb-330">考虑 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="406cb-331">包含 _ViewImports.cshtml 的已更新的 Pages/Create.cshtml 视图文件和前面的布局文件 ：</span><span class="sxs-lookup"><span data-stu-id="406cb-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="406cb-332">在前面的代码中，_ViewImports.cshtml 导入了命名空间和标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="406cb-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="406cb-333">布局文件导入了 JavaScript 文件。</span><span class="sxs-lookup"><span data-stu-id="406cb-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="406cb-334">[Razor Pages 初学者项目](#rpvs17)包含 Pages/_ValidationScriptsPartial.cshtml，它与客户端验证联合。</span><span class="sxs-lookup"><span data-stu-id="406cb-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="406cb-335">有关分部视图的详细信息，请参阅 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="406cb-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="406cb-336">页面的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="406cb-336">URL generation for Pages</span></span>

<span data-ttu-id="406cb-337">之前显示的 `Create` 页面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="406cb-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="406cb-338">应用具有以下文件/文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="406cb-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="406cb-339">/Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-339">*/Pages*</span></span>

  * <span data-ttu-id="406cb-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="406cb-341">Privacy.cshtml</span><span class="sxs-lookup"><span data-stu-id="406cb-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="406cb-342">/Customers</span><span class="sxs-lookup"><span data-stu-id="406cb-342">*/Customers*</span></span>

    * <span data-ttu-id="406cb-343">Create.cshtml</span><span class="sxs-lookup"><span data-stu-id="406cb-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="406cb-344">Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="406cb-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="406cb-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-345">*Index.cshtml*</span></span>

<span data-ttu-id="406cb-346">成功后，Pages/Customers/Create.cshtml and Pages/Customers/Edit.cshtml 页面将重定向到 Pages/Customers/Index.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="406cb-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="406cb-347">字符串 `./Index` 是用于访问前一页的相对页名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="406cb-348">它用于生成 *Pages/Customers/Index.cshtml* 页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="406cb-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="406cb-349">例如：</span><span class="sxs-lookup"><span data-stu-id="406cb-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="406cb-350">绝对页名称 `/Index` 用于生成 *Pages/Index. cshtml* 页面的 URL。</span><span class="sxs-lookup"><span data-stu-id="406cb-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="406cb-351">例如：</span><span class="sxs-lookup"><span data-stu-id="406cb-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="406cb-352">页面名称是从根“/Pages”文件夹到页面的路径（包含前导 `/`，例如 `/Index`）。</span><span class="sxs-lookup"><span data-stu-id="406cb-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="406cb-353">与硬编码 URL 相比，前面的 URL 生成示例提供了改进的选项和功能。</span><span class="sxs-lookup"><span data-stu-id="406cb-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="406cb-354">URL 生成使用[路由](xref:mvc/controllers/routing)，并且可以根据目标路径定义路由的方式生成参数并对参数编码。</span><span class="sxs-lookup"><span data-stu-id="406cb-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="406cb-355">页面的 URL 生成支持相对名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="406cb-356">下表显示了 Pages/Customers/Create.cshtml 中不同的 `RedirectToPage` 参数选择的索引页。</span><span class="sxs-lookup"><span data-stu-id="406cb-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="406cb-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="406cb-357">RedirectToPage(x)</span></span>| <span data-ttu-id="406cb-358">页面</span><span class="sxs-lookup"><span data-stu-id="406cb-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="406cb-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="406cb-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="406cb-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-360">*Pages/Index*</span></span> |
| <span data-ttu-id="406cb-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="406cb-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="406cb-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="406cb-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="406cb-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="406cb-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-364">*Pages/Index*</span></span> |
| <span data-ttu-id="406cb-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="406cb-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="406cb-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="406cb-367">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是相对名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="406cb-368">结合`RedirectToPage` 参数与当前页的路径来计算目标页面的名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="406cb-369">构建结构复杂的站点时，相对名称链接很有用。</span><span class="sxs-lookup"><span data-stu-id="406cb-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="406cb-370">如果使用相对名称链接文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="406cb-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="406cb-371">重命名文件夹不会破坏相对链接。</span><span class="sxs-lookup"><span data-stu-id="406cb-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="406cb-372">链接不会中断，因为它们不包含文件夹名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="406cb-373">若要重定向到不同[区域](xref:mvc/controllers/areas)中的页面，请指定区域：</span><span class="sxs-lookup"><span data-stu-id="406cb-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="406cb-374">有关详细信息，请参阅 <xref:mvc/controllers/areas> 和 <xref:razor-pages/razor-pages-conventions>。</span><span class="sxs-lookup"><span data-stu-id="406cb-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="406cb-375">ViewData 特性</span><span class="sxs-lookup"><span data-stu-id="406cb-375">ViewData attribute</span></span>

<span data-ttu-id="406cb-376">可以通过 <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> 将数据传递到页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="406cb-377">具有 `[ViewData]` 特性的属性从 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> 保存和加载值。</span><span class="sxs-lookup"><span data-stu-id="406cb-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="406cb-378">在下面的示例中，`AboutModel` 将 `[ViewData]` 特性应用于 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="406cb-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="406cb-379">在“关于”页面中，以模型属性的形式访问 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="406cb-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="406cb-380">在布局中，从 ViewData 字典读取标题：</span><span class="sxs-lookup"><span data-stu-id="406cb-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="406cb-381">TempData</span><span class="sxs-lookup"><span data-stu-id="406cb-381">TempData</span></span>

<span data-ttu-id="406cb-382">ASP.NET Core 公开 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>。</span><span class="sxs-lookup"><span data-stu-id="406cb-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="406cb-383">此属性存储未读取的数据。</span><span class="sxs-lookup"><span data-stu-id="406cb-383">This property stores data until it's read.</span></span> <span data-ttu-id="406cb-384"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> 和 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="406cb-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="406cb-385">`TempData` 在多个请求需要数据的情况下对重定向很有用。</span><span class="sxs-lookup"><span data-stu-id="406cb-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="406cb-386">下面的代码使用 `TempData` 设置 `Message` 的值：</span><span class="sxs-lookup"><span data-stu-id="406cb-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="406cb-387">Pages/Customers/Index.cshtml 文件中的以下标记使用 `TempData` 显示 `Message` 的值。</span><span class="sxs-lookup"><span data-stu-id="406cb-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="406cb-388">Pages/Customers/Index.cshtml.cs 页面模型将 `[TempData]` 属性应用到 `Message` 属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="406cb-389">有关详细信息，请参阅 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="406cb-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="406cb-390">针对一个页面的多个处理程序</span><span class="sxs-lookup"><span data-stu-id="406cb-390">Multiple handlers per page</span></span>

<span data-ttu-id="406cb-391">以下页面使用 `asp-page-handler` 标记帮助程序为两个处理程序生成标记：</span><span class="sxs-lookup"><span data-stu-id="406cb-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="406cb-392">前面示例中的窗体包含两个提交按钮，每个提交按钮均使用 `FormActionTagHelper` 提交到不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="406cb-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="406cb-393">`asp-page-handler` 是 `asp-page` 的配套属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="406cb-394">`asp-page-handler` 生成提交到页面定义的各个处理程序方法的 URL。</span><span class="sxs-lookup"><span data-stu-id="406cb-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="406cb-395">未指定 `asp-page`，因为示例已链接到当前页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="406cb-396">页面模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="406cb-397">前面的代码使用已命名处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="406cb-398">已命名处理程序方法通过采用名称中 `On<HTTP Verb>` 之后及 `Async` 之前的文本（如果有）创建。</span><span class="sxs-lookup"><span data-stu-id="406cb-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="406cb-399">在前面的示例中，页面方法是 OnPost **JoinList** Async 和 OnPost **JoinListUC** Async。</span><span class="sxs-lookup"><span data-stu-id="406cb-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="406cb-400">删除 OnPost 和 Async 后，处理程序名称为 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="406cb-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="406cb-401">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="406cb-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="406cb-402">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="406cb-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="406cb-403">自定义路由</span><span class="sxs-lookup"><span data-stu-id="406cb-403">Custom routes</span></span>

<span data-ttu-id="406cb-404">使用 `@page` 指令，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="406cb-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="406cb-405">指定页面的自定义路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-405">Specify a custom route to a page.</span></span> <span data-ttu-id="406cb-406">例如，使用 `@page "/Some/Other/Path"` 将“关于”页面的路由设置为 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="406cb-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="406cb-407">将段追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-407">Append segments to a page's default route.</span></span> <span data-ttu-id="406cb-408">例如，可使用 `@page "item"` 将“item”段添加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="406cb-409">将参数追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="406cb-410">例如，`@page "{id}"` 页面需要 ID 参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="406cb-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="406cb-411">支持开头处以波形符 (`~`) 指定的相对于根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="406cb-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="406cb-412">例如，`@page "~/Some/Other/Path"` 和 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="406cb-413">如果你不喜欢 URL 中的查询字符串 `?handler=JoinList`，请更改路由，将处理程序名称放在 URL 的路径部分。</span><span class="sxs-lookup"><span data-stu-id="406cb-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="406cb-414">可以通过在 `@page` 指令后面添加使用双引号括起来的路由模板来自定义路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="406cb-415">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="406cb-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="406cb-416">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="406cb-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="406cb-417">`handler` 前面的 `?` 表示路由参数为可选。</span><span class="sxs-lookup"><span data-stu-id="406cb-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="406cb-418">高级配置和设置</span><span class="sxs-lookup"><span data-stu-id="406cb-418">Advanced configuration and settings</span></span>

<span data-ttu-id="406cb-419">大多数应用不需要以下部分中的配置和设置。</span><span class="sxs-lookup"><span data-stu-id="406cb-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="406cb-420">要配置高级选项，请使用 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 重载，该重载配置 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>：</span><span class="sxs-lookup"><span data-stu-id="406cb-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="406cb-421">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> 设置页面的根目录，或者为页面添加应用程序模型约定。</span><span class="sxs-lookup"><span data-stu-id="406cb-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="406cb-422">有关约定的详细信息，请参阅 [Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="406cb-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="406cb-423">若要预编译视图，请参阅 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="406cb-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="406cb-424">指定 Razor Pages 位于内容根目录中</span><span class="sxs-lookup"><span data-stu-id="406cb-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="406cb-425">默认情况下，Razor Pages 位于 /Pages 目录的根位置。</span><span class="sxs-lookup"><span data-stu-id="406cb-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="406cb-426">添加 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> 以指定 Razor Pages 位于应用的[内容根](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>)：</span><span class="sxs-lookup"><span data-stu-id="406cb-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="406cb-427">指定 Razor Pages 位于自定义根目录中</span><span class="sxs-lookup"><span data-stu-id="406cb-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="406cb-428">添加 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>，以指定 Razor Pages 位于应用中自定义根目录位置（提供相对路径）：</span><span class="sxs-lookup"><span data-stu-id="406cb-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="406cb-429">其他资源</span><span class="sxs-lookup"><span data-stu-id="406cb-429">Additional resources</span></span>

* <span data-ttu-id="406cb-430">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)这篇文章以本文为基础撰写。</span><span class="sxs-lookup"><span data-stu-id="406cb-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="406cb-431">授权属性和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="406cb-432">下载或查看示例代码</span><span class="sxs-lookup"><span data-stu-id="406cb-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* [<span data-ttu-id="406cb-433">ASP.NET Core 的 Razor 语法参考</span><span class="sxs-lookup"><span data-stu-id="406cb-433">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="406cb-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cb-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="406cb-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cb-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="406cb-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="406cb-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="406cb-437">创建 Razor Pages 项目</span><span class="sxs-lookup"><span data-stu-id="406cb-437">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="406cb-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="406cb-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="406cb-439">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)，获取关于如何创建 Razor Pages 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="406cb-439">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="406cb-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="406cb-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="406cb-441">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="406cb-441">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="406cb-442">在 Visual Studio for Mac 中打开生成的 .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="406cb-442">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="406cb-443">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="406cb-443">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="406cb-444">在命令行中运行 `dotnet new webapp`。</span><span class="sxs-lookup"><span data-stu-id="406cb-444">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="406cb-445">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-445">Razor Pages</span></span>

<span data-ttu-id="406cb-446">Startup.cs 中已启用 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="406cb-446">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="406cb-447">请考虑一个基本页面：<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="406cb-447">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="406cb-448">前面的代码与具有控制器和视图的 ASP.NET Core 应用中使用的 [Razor 视图文件](xref:tutorials/first-mvc-app/adding-view)非常相似。</span><span class="sxs-lookup"><span data-stu-id="406cb-448">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="406cb-449">不同之处在于 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="406cb-449">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="406cb-450">`@page` 使文件转换为一个 MVC 操作 ，这意味着它将直接处理请求，而无需通过控制器处理。</span><span class="sxs-lookup"><span data-stu-id="406cb-450">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="406cb-451">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="406cb-451">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="406cb-452">`@page` 会影响其他 Razor 构造的行为。</span><span class="sxs-lookup"><span data-stu-id="406cb-452">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="406cb-453">将在以下两个文件中显示使用 `PageModel` 类的类似页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-453">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="406cb-454">Pages/Index2.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-454">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="406cb-455">Pages/Index2.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-455">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="406cb-456">按照惯例，`PageModel` 类文件的名称与追加 .cs 的 Razor Page 文件名称相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-456">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="406cb-457">例如，前面的 Razor Page 的名称为 Pages/Index2.cshtml。</span><span class="sxs-lookup"><span data-stu-id="406cb-457">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="406cb-458">包含 `PageModel` 类的文件的名称为 Pages/Index2.cshtml.cs。</span><span class="sxs-lookup"><span data-stu-id="406cb-458">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="406cb-459">页面的 URL 路径的关联由页面在文件系统中的位置决定。</span><span class="sxs-lookup"><span data-stu-id="406cb-459">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="406cb-460">下表显示了 Razor Page 路径及匹配的 URL：</span><span class="sxs-lookup"><span data-stu-id="406cb-460">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="406cb-461">文件名和路径</span><span class="sxs-lookup"><span data-stu-id="406cb-461">File name and path</span></span>               | <span data-ttu-id="406cb-462">匹配的 URL</span><span class="sxs-lookup"><span data-stu-id="406cb-462">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="406cb-463">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-463">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="406cb-464">`/` 或 `/Index`</span><span class="sxs-lookup"><span data-stu-id="406cb-464">`/` or `/Index`</span></span> |
| <span data-ttu-id="406cb-465">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-465">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="406cb-466">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-466">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="406cb-467">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-467">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="406cb-468">`/Store` 或 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="406cb-468">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="406cb-469">注意：</span><span class="sxs-lookup"><span data-stu-id="406cb-469">Notes:</span></span>

* <span data-ttu-id="406cb-470">默认情况下，运行时在“Pages”文件夹中查找 Razor Pages 文件。</span><span class="sxs-lookup"><span data-stu-id="406cb-470">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="406cb-471">URL 未包含页面时，`Index` 为默认页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-471">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="406cb-472">编写基本窗体</span><span class="sxs-lookup"><span data-stu-id="406cb-472">Write a basic form</span></span>

<span data-ttu-id="406cb-473">由于 Razor Pages 的设计，在构建应用时可轻松实施用于 Web 浏览器的常用模式。</span><span class="sxs-lookup"><span data-stu-id="406cb-473">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="406cb-474">[模型绑定](xref:mvc/models/model-binding)、[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 HTML 帮助程序均只可用于 Razor Page 类中定义的属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-474">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="406cb-475">请参考为 `Contact` 模型实现的基本的“联系我们”窗体页面：</span><span class="sxs-lookup"><span data-stu-id="406cb-475">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="406cb-476">在本文档中的示例中，`DbContext` 在 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 文件中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="406cb-476">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="406cb-477">数据模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-477">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="406cb-478">数据库上下文：</span><span class="sxs-lookup"><span data-stu-id="406cb-478">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="406cb-479">Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-479">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="406cb-480">Pages/Create.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-480">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="406cb-481">按照惯例，`PageModel` 类命名为 `<PageName>Model`并且它与页面位于同一个命名空间中。</span><span class="sxs-lookup"><span data-stu-id="406cb-481">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="406cb-482">使用 `PageModel` 类，可以将页面的逻辑与其展示分离开来。</span><span class="sxs-lookup"><span data-stu-id="406cb-482">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="406cb-483">它定义了页面处理程序，用于处理发送到页面的请求和用于呈现页面的数据。</span><span class="sxs-lookup"><span data-stu-id="406cb-483">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="406cb-484">这种隔离可实现：</span><span class="sxs-lookup"><span data-stu-id="406cb-484">This separation allows:</span></span>

* <span data-ttu-id="406cb-485">通过[依赖关系注入](xref:fundamentals/dependency-injection)管理页面依赖项。</span><span class="sxs-lookup"><span data-stu-id="406cb-485">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="406cb-486">对页面执行[单元测试](xref:test/razor-pages-tests)。</span><span class="sxs-lookup"><span data-stu-id="406cb-486">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="406cb-487">页面包含 `OnPostAsync` 处理程序方法，它在 `POST` 请求上运行（当用户发布窗体时）。</span><span class="sxs-lookup"><span data-stu-id="406cb-487">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="406cb-488">可以为任何 HTTP 谓词添加处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-488">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="406cb-489">最常见的处理程序是：</span><span class="sxs-lookup"><span data-stu-id="406cb-489">The most common handlers are:</span></span>

* <span data-ttu-id="406cb-490">`OnGet`，用于初始化页面所需的状态。</span><span class="sxs-lookup"><span data-stu-id="406cb-490">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="406cb-491">[OnGet](#OnGet) 示例。</span><span class="sxs-lookup"><span data-stu-id="406cb-491">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="406cb-492">`OnPost`，用于处理窗体提交。</span><span class="sxs-lookup"><span data-stu-id="406cb-492">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="406cb-493">`Async` 命名后缀为可选，但是按照惯例通常会将它用于异步函数。</span><span class="sxs-lookup"><span data-stu-id="406cb-493">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="406cb-494">前面的代码通常用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="406cb-494">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="406cb-495">如果你熟悉使用控制器和视图的 ASP.NET 应用：</span><span class="sxs-lookup"><span data-stu-id="406cb-495">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="406cb-496">前面示例中的 `OnPostAsync` 代码类似于典型的控制器代码。</span><span class="sxs-lookup"><span data-stu-id="406cb-496">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="406cb-497">多数 MVC 基元（例如[模型绑定](xref:mvc/models/model-binding)、[验证](xref:mvc/models/validation)、[验证](xref:mvc/models/validation)和操作结果）都是共享的。</span><span class="sxs-lookup"><span data-stu-id="406cb-497">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="406cb-498">之前的 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="406cb-498">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="406cb-499">`OnPostAsync` 的基本流：</span><span class="sxs-lookup"><span data-stu-id="406cb-499">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="406cb-500">检查验证错误。</span><span class="sxs-lookup"><span data-stu-id="406cb-500">Check for validation errors.</span></span>

* <span data-ttu-id="406cb-501">如果没有错误，则保存数据并重定向。</span><span class="sxs-lookup"><span data-stu-id="406cb-501">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="406cb-502">如果有错误，则再次显示页面并附带验证消息。</span><span class="sxs-lookup"><span data-stu-id="406cb-502">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="406cb-503">客户端验证与传统的 ASP.NET Core MVC 应用程序相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-503">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="406cb-504">很多情况下，都会在客户端上检测到验证错误，并且从不将它们提交到服务器。</span><span class="sxs-lookup"><span data-stu-id="406cb-504">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="406cb-505">成功输入数据后，`OnPostAsync` 处理程序方法调用 `RedirectToPage` 帮助程序方法来返回 `RedirectToPageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="406cb-505">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="406cb-506">`RedirectToPage` 是新的操作结果，类似于 `RedirectToAction` 或 `RedirectToRoute`，但是已针对页面进行自定义。</span><span class="sxs-lookup"><span data-stu-id="406cb-506">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="406cb-507">在前面的示例中，它将重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="406cb-507">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="406cb-508">[页面 URL 生成](#url_gen)部分中详细介绍了 `RedirectToPage`。</span><span class="sxs-lookup"><span data-stu-id="406cb-508">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="406cb-509">提交的窗体存在（已传递到服务器的）验证错误时，`OnPostAsync` 处理程序方法调用 `Page` 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-509">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="406cb-510">`Page` 返回 `PageResult` 的实例。</span><span class="sxs-lookup"><span data-stu-id="406cb-510">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="406cb-511">返回 `Page` 的过程与控制器中的操作返回 `View` 的过程相似。</span><span class="sxs-lookup"><span data-stu-id="406cb-511">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="406cb-512">`PageResult` 是处理程序方法的默认返回类型。</span><span class="sxs-lookup"><span data-stu-id="406cb-512">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="406cb-513">返回 `void` 的处理程序方法将显示页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-513">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="406cb-514">`Customer` 属性使用 `[BindProperty]` 特性来选择加入模型绑定。</span><span class="sxs-lookup"><span data-stu-id="406cb-514">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="406cb-515">Razor Pages 只绑定带有非 `GET` 谓词的属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-515">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="406cb-516">绑定属性可以减少需要编写的代码量。</span><span class="sxs-lookup"><span data-stu-id="406cb-516">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="406cb-517">绑定通过使用相同的属性显示窗体字段 (`<input asp-for="Customer.Name">`) 来减少代码，并接受输入。</span><span class="sxs-lookup"><span data-stu-id="406cb-517">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="406cb-518">主页 (Index.cshtml)：</span><span class="sxs-lookup"><span data-stu-id="406cb-518">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="406cb-519">关联的 `PageModel` 类 (Index.cshtml.cs)：</span><span class="sxs-lookup"><span data-stu-id="406cb-519">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="406cb-520">Index.cshtml 文件包含以下标记来创建每个联系人项的编辑链接：</span><span class="sxs-lookup"><span data-stu-id="406cb-520">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="406cb-521">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)使用 `asp-route-{value}` 属性生成“编辑”页面的链接。</span><span class="sxs-lookup"><span data-stu-id="406cb-521">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="406cb-522">此链接包含路由数据及联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="406cb-522">The link contains route data with the contact ID.</span></span> <span data-ttu-id="406cb-523">例如 `https://localhost:5001/Edit/1`。</span><span class="sxs-lookup"><span data-stu-id="406cb-523">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="406cb-524">[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="406cb-524">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="406cb-525">标记帮助程序由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 启动</span><span class="sxs-lookup"><span data-stu-id="406cb-525">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="406cb-526">Pages/Edit.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-526">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="406cb-527">第一行包含 `@page "{id:int}"` 指令。</span><span class="sxs-lookup"><span data-stu-id="406cb-527">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="406cb-528">路由约束 `"{id:int}"` 告诉页面接受包含 `int` 路由数据的页面请求。</span><span class="sxs-lookup"><span data-stu-id="406cb-528">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="406cb-529">如果页面请求未包含可转换为 `int` 的路由数据，则运行时返回 HTTP 404（未找到）错误。</span><span class="sxs-lookup"><span data-stu-id="406cb-529">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="406cb-530">若要使 ID 可选，请将 `?` 追加到路由约束：</span><span class="sxs-lookup"><span data-stu-id="406cb-530">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="406cb-531">Pages/Edit.cshtml.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-531">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="406cb-532">Index.cshtml 文件还包含用于为每个客户联系人创建删除按钮的标记：</span><span class="sxs-lookup"><span data-stu-id="406cb-532">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="406cb-533">删除按钮采用 HTML 呈现，其 `formaction` 包括参数：</span><span class="sxs-lookup"><span data-stu-id="406cb-533">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="406cb-534">`asp-route-id` 属性指定的客户联系人 ID。</span><span class="sxs-lookup"><span data-stu-id="406cb-534">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="406cb-535">`asp-page-handler` 属性指定的 `handler`。</span><span class="sxs-lookup"><span data-stu-id="406cb-535">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="406cb-536">下面是呈现的删除按钮的示例，其中客户联系人 ID 为 `1`：</span><span class="sxs-lookup"><span data-stu-id="406cb-536">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="406cb-537">选中按钮时，向服务器发送窗体 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="406cb-537">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="406cb-538">按照惯例，根据方案 `OnPost[handler]Async` 基于 `handler` 参数的值来选择处理程序方法的名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-538">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="406cb-539">因为本示例中 `handler` 是 `delete`，因此 `OnPostDeleteAsync` 处理程序方法用于处理 `POST` 请求。</span><span class="sxs-lookup"><span data-stu-id="406cb-539">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="406cb-540">如果 `asp-page-handler` 设置为其他值（如 `remove`），则选择名称为 `OnPostRemoveAsync` 的处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-540">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="406cb-541">以下代码显示了 `OnPostDeleteAsync` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="406cb-541">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="406cb-542">`OnPostDeleteAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="406cb-542">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="406cb-543">接受来自查询字符串的 `id`。</span><span class="sxs-lookup"><span data-stu-id="406cb-543">Accepts the `id` from the query string.</span></span> <span data-ttu-id="406cb-544">如果 Index.cshtml 页面指令包含路由约束 `"{id:int?}"`，则 `id` 来自路由数据。</span><span class="sxs-lookup"><span data-stu-id="406cb-544">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="406cb-545">`id` 的路由数据在 `https://localhost:5001/Customers/2` 等 URI 中指定。</span><span class="sxs-lookup"><span data-stu-id="406cb-545">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="406cb-546">使用 `FindAsync` 查询客户联系人的数据库。</span><span class="sxs-lookup"><span data-stu-id="406cb-546">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="406cb-547">如果找到客户联系人，则从客户联系人列表将其删除。</span><span class="sxs-lookup"><span data-stu-id="406cb-547">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="406cb-548">数据库将更新。</span><span class="sxs-lookup"><span data-stu-id="406cb-548">The database is updated.</span></span>
* <span data-ttu-id="406cb-549">调用 `RedirectToPage`，重定向到根索引页 (`/Index`)。</span><span class="sxs-lookup"><span data-stu-id="406cb-549">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="406cb-550">按需标记页面属性</span><span class="sxs-lookup"><span data-stu-id="406cb-550">Mark page properties as required</span></span>

<span data-ttu-id="406cb-551">`PageModel` 上的属性可通过 [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) 特性进行标记：</span><span class="sxs-lookup"><span data-stu-id="406cb-551">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="406cb-552">有关详细信息，请参阅[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="406cb-552">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="406cb-553">使用 OnGet 处理程序回退来处理 HEAD 请求</span><span class="sxs-lookup"><span data-stu-id="406cb-553">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="406cb-554">`HEAD` 请求可检索特定资源的标头。</span><span class="sxs-lookup"><span data-stu-id="406cb-554">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="406cb-555">与 `GET` 请求不同，`HEAD` 请求不返回响应正文。</span><span class="sxs-lookup"><span data-stu-id="406cb-555">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="406cb-556">通常，针对 `HEAD` 请求创建和调用 `OnHead` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="406cb-556">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="406cb-557">在 ASP.NET Core 2.1 或更高版本中，如果未定义 `OnHead` 处理程序，则 Razor Pages 会回退到调用 `OnGet` 处理程序。</span><span class="sxs-lookup"><span data-stu-id="406cb-557">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="406cb-558">此行为是通过在 `Startup.ConfigureServices` 中调用 [SetCompatibilityVersion](xref:mvc/compatibility-version) 而启用的：</span><span class="sxs-lookup"><span data-stu-id="406cb-558">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="406cb-559">默认模板在 ASP.NET Core 2.1 和 2.2 中生成 `SetCompatibilityVersion` 调用。</span><span class="sxs-lookup"><span data-stu-id="406cb-559">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="406cb-560">`SetCompatibilityVersion` 有效地将 Razor Pages 选项 `AllowMappingHeadRequestsToGetHandler` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="406cb-560">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="406cb-561">可显式选择使用特定行为，而不是通过 `SetCompatibilityVersion` 选择使用所有行为。</span><span class="sxs-lookup"><span data-stu-id="406cb-561">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="406cb-562">通过下列代码，可选择允许将 `HEAD` 请求映射到 `OnGet` 处理程序：</span><span class="sxs-lookup"><span data-stu-id="406cb-562">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="406cb-563">XSRF/CSRF 和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-563">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="406cb-564">无需为[防伪验证](xref:security/anti-request-forgery)编写任何代码。</span><span class="sxs-lookup"><span data-stu-id="406cb-564">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="406cb-565">Razor Pages 自动将防伪标记生成过程和验证过程包含在内。</span><span class="sxs-lookup"><span data-stu-id="406cb-565">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="406cb-566">将布局、分区、模板和标记帮助程序用于 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-566">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="406cb-567">页面可使用 Razor 视图引擎的所有功能。</span><span class="sxs-lookup"><span data-stu-id="406cb-567">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="406cb-568">布局、分区、模板、标记帮助程序、_ViewStart.cshtml 和 _ViewImports.cshtml 的工作方式与它们在传统的 Razor 视图中的工作方式相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-568">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="406cb-569">让我们使用其中的一些功能来整理此页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-569">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="406cb-570">向 Pages/Shared/_Layout.cshtml 添加[布局页面](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="406cb-570">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="406cb-571">[布局](xref:mvc/views/layout)：</span><span class="sxs-lookup"><span data-stu-id="406cb-571">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="406cb-572">控制每个页面的布局（页面选择退出布局时除外）。</span><span class="sxs-lookup"><span data-stu-id="406cb-572">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="406cb-573">导入 HTML 结构，例如 JavaScript 和样式表。</span><span class="sxs-lookup"><span data-stu-id="406cb-573">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="406cb-574">请参阅[布局页面](xref:mvc/views/layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="406cb-574">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="406cb-575">在 Pages/_ViewStart.cshtml 中设置 [Layout](xref:mvc/views/layout#specifying-a-layout) 属性：</span><span class="sxs-lookup"><span data-stu-id="406cb-575">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="406cb-576">布局位于“页面/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="406cb-576">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="406cb-577">页面按层次结构从当前页面的文件夹开始查找其他视图（布局、模板、分区）。</span><span class="sxs-lookup"><span data-stu-id="406cb-577">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="406cb-578">可以从“Pages”文件夹下的任意 Razor 页面使用“Pages/Shared”文件夹中的布局。</span><span class="sxs-lookup"><span data-stu-id="406cb-578">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="406cb-579">布局文件应位于 Pages/Shared 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="406cb-579">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="406cb-580">建议不要将布局文件放在“视图/共享”文件夹中。</span><span class="sxs-lookup"><span data-stu-id="406cb-580">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="406cb-581">视图/共享 是一种 MVC 视图模式。</span><span class="sxs-lookup"><span data-stu-id="406cb-581">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="406cb-582">Razor Pages 旨在依赖文件夹层次结构，而非路径约定。</span><span class="sxs-lookup"><span data-stu-id="406cb-582">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="406cb-583">Razor Page 中的视图搜索包含“Pages”文件夹。</span><span class="sxs-lookup"><span data-stu-id="406cb-583">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="406cb-584">要用于 MVC 控制器和传统 Razor 视图的布局、模板和分区可正常运行。</span><span class="sxs-lookup"><span data-stu-id="406cb-584">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="406cb-585">添加 Pages/_ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-585">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="406cb-586">本教程的后续部分中将介绍 `@namespace`。</span><span class="sxs-lookup"><span data-stu-id="406cb-586">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="406cb-587">`@addTagHelper` 指令将[内置标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/Index)引入“页面”文件夹中的所有页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-587">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="406cb-588">页面上显式使用 `@namespace` 指令后：</span><span class="sxs-lookup"><span data-stu-id="406cb-588">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="406cb-589">此指令将为页面设置命名空间。</span><span class="sxs-lookup"><span data-stu-id="406cb-589">The directive sets the namespace for the page.</span></span> <span data-ttu-id="406cb-590">`@model` 指令无需包含命名空间。</span><span class="sxs-lookup"><span data-stu-id="406cb-590">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="406cb-591">_ViewImports.cshtml 中包含 `@namespace` 指令后，指定的命名空间将为在导入 `@namespace` 指令的页面中生成的命名空间提供前缀。</span><span class="sxs-lookup"><span data-stu-id="406cb-591">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="406cb-592">生成的命名空间的剩余部分（后缀部分）是包含 _ViewImports.cshtml 的文件夹与包含页面的文件夹之间以点分隔的相对路径。</span><span class="sxs-lookup"><span data-stu-id="406cb-592">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="406cb-593">例如，`PageModel` 类 Pages/Customers/Edit.cshtml.cs 显式设置命名空间：</span><span class="sxs-lookup"><span data-stu-id="406cb-593">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="406cb-594">Pages/_ViewImports.cshtml 文件设置以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="406cb-594">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="406cb-595">为 Pages/Customers/Edit.cshtml Razor Page 生成的命名空间与 `PageModel` 类相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-595">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="406cb-596">`@namespace`  *也适用于传统 Razor 视图。*</span><span class="sxs-lookup"><span data-stu-id="406cb-596">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="406cb-597">原始的 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-597">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="406cb-598">更新后的 Pages/Create.cshtml 视图文件：</span><span class="sxs-lookup"><span data-stu-id="406cb-598">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="406cb-599">[Razor Pages 初学者项目](#rpvs17)包含 Pages/_ValidationScriptsPartial.cshtml，它与客户端验证联合。</span><span class="sxs-lookup"><span data-stu-id="406cb-599">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="406cb-600">有关分部视图的详细信息，请参阅 <xref:mvc/views/partial>。</span><span class="sxs-lookup"><span data-stu-id="406cb-600">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="406cb-601">页面的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="406cb-601">URL generation for Pages</span></span>

<span data-ttu-id="406cb-602">之前显示的 `Create` 页面使用 `RedirectToPage`：</span><span class="sxs-lookup"><span data-stu-id="406cb-602">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="406cb-603">应用具有以下文件/文件夹结构：</span><span class="sxs-lookup"><span data-stu-id="406cb-603">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="406cb-604">/Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-604">*/Pages*</span></span>

  * <span data-ttu-id="406cb-605">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-605">*Index.cshtml*</span></span>
  * <span data-ttu-id="406cb-606">/Customers</span><span class="sxs-lookup"><span data-stu-id="406cb-606">*/Customers*</span></span>

    * <span data-ttu-id="406cb-607">Create.cshtml</span><span class="sxs-lookup"><span data-stu-id="406cb-607">*Create.cshtml*</span></span>
    * <span data-ttu-id="406cb-608">Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="406cb-608">*Edit.cshtml*</span></span>
    * <span data-ttu-id="406cb-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="406cb-609">*Index.cshtml*</span></span>

<span data-ttu-id="406cb-610">成功后，Pages/Customers/Create.cshtml 和 Pages/Customers/Edit.cshtml 页面将重定向到 Pages/Index.cshtml。</span><span class="sxs-lookup"><span data-stu-id="406cb-610">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="406cb-611">字符串 `/Index` 是用于访问上一页的 URI 的组成部分。</span><span class="sxs-lookup"><span data-stu-id="406cb-611">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="406cb-612">可以使用字符串 `/Index` 生成 Pages/Index.cshtml 页面的 URI。</span><span class="sxs-lookup"><span data-stu-id="406cb-612">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="406cb-613">例如：</span><span class="sxs-lookup"><span data-stu-id="406cb-613">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="406cb-614">页面名称是从根“/Pages”文件夹到页面的路径（包含前导 `/`，例如 `/Index`）。</span><span class="sxs-lookup"><span data-stu-id="406cb-614">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="406cb-615">与硬编码 URL 相比，前面的 URL 生成示例提供了改进的选项和功能。</span><span class="sxs-lookup"><span data-stu-id="406cb-615">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="406cb-616">URL 生成使用[路由](xref:mvc/controllers/routing)，并且可以根据目标路径定义路由的方式生成参数并对参数编码。</span><span class="sxs-lookup"><span data-stu-id="406cb-616">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="406cb-617">页面的 URL 生成支持相对名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-617">URL generation for pages supports relative names.</span></span> <span data-ttu-id="406cb-618">下表显示了 Pages/Customers/Create.cshtml 中不同的 `RedirectToPage` 参数选择的索引页：</span><span class="sxs-lookup"><span data-stu-id="406cb-618">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="406cb-619">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="406cb-619">RedirectToPage(x)</span></span>| <span data-ttu-id="406cb-620">页面</span><span class="sxs-lookup"><span data-stu-id="406cb-620">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="406cb-621">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="406cb-621">RedirectToPage("/Index")</span></span> | <span data-ttu-id="406cb-622">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-622">*Pages/Index*</span></span> |
| <span data-ttu-id="406cb-623">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="406cb-623">RedirectToPage("./Index");</span></span> | <span data-ttu-id="406cb-624">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-624">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="406cb-625">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="406cb-625">RedirectToPage("../Index")</span></span> | <span data-ttu-id="406cb-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-626">*Pages/Index*</span></span> |
| <span data-ttu-id="406cb-627">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="406cb-627">RedirectToPage("Index")</span></span>  | <span data-ttu-id="406cb-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="406cb-628">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="406cb-629">`RedirectToPage("Index")`、`RedirectToPage("./Index")` 和 `RedirectToPage("../Index")` 是相对名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="406cb-630">结合`RedirectToPage` 参数与当前页的路径来计算目标页面的名称。</span><span class="sxs-lookup"><span data-stu-id="406cb-630">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="406cb-631">构建结构复杂的站点时，相对名称链接很有用。</span><span class="sxs-lookup"><span data-stu-id="406cb-631">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="406cb-632">如果使用相对名称链接文件夹中的页面，则可以重命名该文件夹。</span><span class="sxs-lookup"><span data-stu-id="406cb-632">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="406cb-633">所有链接仍然有效（因为这些链接未包含此文件夹名称）。</span><span class="sxs-lookup"><span data-stu-id="406cb-633">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="406cb-634">若要重定向到不同[区域](xref:mvc/controllers/areas)中的页面，请指定区域：</span><span class="sxs-lookup"><span data-stu-id="406cb-634">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="406cb-635">有关详细信息，请参阅 <xref:mvc/controllers/areas>。</span><span class="sxs-lookup"><span data-stu-id="406cb-635">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="406cb-636">ViewData 特性</span><span class="sxs-lookup"><span data-stu-id="406cb-636">ViewData attribute</span></span>

<span data-ttu-id="406cb-637">可以通过 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) 将数据传递到页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-637">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="406cb-638">控制器或 Razor Pages 模型上使用 `[ViewData]` 属性的属性将其值存储在 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 中并从此处进行加载。</span><span class="sxs-lookup"><span data-stu-id="406cb-638">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="406cb-639">在下面的示例中，`AboutModel` 包含使用 `[ViewData]` 标记的 `Title` 属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-639">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="406cb-640">`Title` 属性设置为“关于”页面的标题：</span><span class="sxs-lookup"><span data-stu-id="406cb-640">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="406cb-641">在“关于”页面中，以模型属性的形式访问 `Title` 属性：</span><span class="sxs-lookup"><span data-stu-id="406cb-641">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="406cb-642">在布局中，从 ViewData 字典读取标题：</span><span class="sxs-lookup"><span data-stu-id="406cb-642">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="406cb-643">TempData</span><span class="sxs-lookup"><span data-stu-id="406cb-643">TempData</span></span>

<span data-ttu-id="406cb-644">ASP.NET 在[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)上公开了 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) 属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-644">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="406cb-645">此属性存储未读取的数据。</span><span class="sxs-lookup"><span data-stu-id="406cb-645">This property stores data until it's read.</span></span> <span data-ttu-id="406cb-646">`Keep` 和 `Peek` 方法可用于检查数据，而不执行删除。</span><span class="sxs-lookup"><span data-stu-id="406cb-646">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="406cb-647">多个请求需要数据时，`TempData` 有助于进行重定向。</span><span class="sxs-lookup"><span data-stu-id="406cb-647">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="406cb-648">下面的代码使用 `TempData` 设置 `Message` 的值：</span><span class="sxs-lookup"><span data-stu-id="406cb-648">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="406cb-649">Pages/Customers/Index.cshtml 文件中的以下标记使用 `TempData` 显示 `Message` 的值。</span><span class="sxs-lookup"><span data-stu-id="406cb-649">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="406cb-650">Pages/Customers/Index.cshtml.cs 页面模型将 `[TempData]` 属性应用到 `Message` 属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-650">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="406cb-651">有关详细信息，请参阅 [TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="406cb-651">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="406cb-652">针对一个页面的多个处理程序</span><span class="sxs-lookup"><span data-stu-id="406cb-652">Multiple handlers per page</span></span>

<span data-ttu-id="406cb-653">以下页面使用 `asp-page-handler` 标记帮助程序为两个处理程序生成标记：</span><span class="sxs-lookup"><span data-stu-id="406cb-653">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="406cb-654">前面示例中的窗体包含两个提交按钮，每个提交按钮均使用 `FormActionTagHelper` 提交到不同的 URL。</span><span class="sxs-lookup"><span data-stu-id="406cb-654">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="406cb-655">`asp-page-handler` 是 `asp-page` 的配套属性。</span><span class="sxs-lookup"><span data-stu-id="406cb-655">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="406cb-656">`asp-page-handler` 生成提交到页面定义的各个处理程序方法的 URL。</span><span class="sxs-lookup"><span data-stu-id="406cb-656">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="406cb-657">未指定 `asp-page`，因为示例已链接到当前页面。</span><span class="sxs-lookup"><span data-stu-id="406cb-657">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="406cb-658">页面模型：</span><span class="sxs-lookup"><span data-stu-id="406cb-658">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="406cb-659">前面的代码使用已命名处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="406cb-659">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="406cb-660">已命名处理程序方法通过采用名称中 `On<HTTP Verb>` 之后及 `Async` 之前的文本（如果有）创建。</span><span class="sxs-lookup"><span data-stu-id="406cb-660">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="406cb-661">在前面的示例中，页面方法是 OnPost **JoinList** Async 和 OnPost **JoinListUC** Async。</span><span class="sxs-lookup"><span data-stu-id="406cb-661">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="406cb-662">删除 OnPost 和 Async 后，处理程序名称为 `JoinList` 和 `JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="406cb-662">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="406cb-663">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`。</span><span class="sxs-lookup"><span data-stu-id="406cb-663">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="406cb-664">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="406cb-664">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="406cb-665">自定义路由</span><span class="sxs-lookup"><span data-stu-id="406cb-665">Custom routes</span></span>

<span data-ttu-id="406cb-666">使用 `@page` 指令，执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="406cb-666">Use the `@page` directive to:</span></span>

* <span data-ttu-id="406cb-667">指定页面的自定义路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-667">Specify a custom route to a page.</span></span> <span data-ttu-id="406cb-668">例如，使用 `@page "/Some/Other/Path"` 将“关于”页面的路由设置为 `/Some/Other/Path`。</span><span class="sxs-lookup"><span data-stu-id="406cb-668">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="406cb-669">将段追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-669">Append segments to a page's default route.</span></span> <span data-ttu-id="406cb-670">例如，可使用 `@page "item"` 将“item”段添加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-670">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="406cb-671">将参数追加到页面的默认路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-671">Append parameters to a page's default route.</span></span> <span data-ttu-id="406cb-672">例如，`@page "{id}"` 页面需要 ID 参数 `id`。</span><span class="sxs-lookup"><span data-stu-id="406cb-672">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="406cb-673">支持开头处以波形符 (`~`) 指定的相对于根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="406cb-673">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="406cb-674">例如，`@page "~/Some/Other/Path"` 和 `@page "/Some/Other/Path"` 相同。</span><span class="sxs-lookup"><span data-stu-id="406cb-674">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="406cb-675">如果你不喜欢 URL 中的查询字符串 `?handler=JoinList`，请更改路由，将处理程序名称放在 URL 的路径部分。</span><span class="sxs-lookup"><span data-stu-id="406cb-675">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="406cb-676">可以通过在 `@page` 指令后面添加使用双引号括起来的路由模板来自定义路由。</span><span class="sxs-lookup"><span data-stu-id="406cb-676">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="406cb-677">使用前面的代码时，提交到 `OnPostJoinListAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinList`。</span><span class="sxs-lookup"><span data-stu-id="406cb-677">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="406cb-678">提交到 `OnPostJoinListUCAsync` 的 URL 路径为 `https://localhost:5001/Customers/CreateFATH/JoinListUC`。</span><span class="sxs-lookup"><span data-stu-id="406cb-678">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="406cb-679">`handler` 前面的 `?` 表示路由参数为可选。</span><span class="sxs-lookup"><span data-stu-id="406cb-679">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="406cb-680">配置和设置</span><span class="sxs-lookup"><span data-stu-id="406cb-680">Configuration and settings</span></span>

<span data-ttu-id="406cb-681">若要配置高级选项，请在 MVC 生成器上使用 `AddRazorPagesOptions` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="406cb-681">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="406cb-682">目前，可以使用 `RazorPagesOptions` 设置页面的根目录，或者为页面添加应用程序模型约定。</span><span class="sxs-lookup"><span data-stu-id="406cb-682">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="406cb-683">通过这种方式，我们在将来会实现更多扩展功能。</span><span class="sxs-lookup"><span data-stu-id="406cb-683">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="406cb-684">若要预编译视图，请参阅 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="406cb-684">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="406cb-685">[下载或查看示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="406cb-685">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="406cb-686">请参阅 [Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)这篇文章以本文为基础撰写。</span><span class="sxs-lookup"><span data-stu-id="406cb-686">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="406cb-687">指定 Razor Pages 位于内容根目录中</span><span class="sxs-lookup"><span data-stu-id="406cb-687">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="406cb-688">默认情况下，Razor Pages 位于 /Pages 目录的根位置。</span><span class="sxs-lookup"><span data-stu-id="406cb-688">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="406cb-689">向 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 添加 [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot)，以指定 Razor Pages 位于应用的[根目录](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath))：</span><span class="sxs-lookup"><span data-stu-id="406cb-689">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="406cb-690">指定 Razor Pages 位于自定义根目录中</span><span class="sxs-lookup"><span data-stu-id="406cb-690">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="406cb-691">向 [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 添加 [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot)，以指定 Razor Pages 位于应用中的自定义根目录（提供相对路径）：</span><span class="sxs-lookup"><span data-stu-id="406cb-691">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="406cb-692">其他资源</span><span class="sxs-lookup"><span data-stu-id="406cb-692">Additional resources</span></span>

* [<span data-ttu-id="406cb-693">授权属性和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="406cb-693">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* [<span data-ttu-id="406cb-694">ASP.NET Core 的 Razor 语法参考</span><span class="sxs-lookup"><span data-stu-id="406cb-694">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
