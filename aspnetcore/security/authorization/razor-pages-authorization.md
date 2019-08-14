---
title: 在 ASP.NET Core razor 页授权约定
author: guardrex
description: 了解如何使用用于授权用户和允许匿名用户访问页面或文件夹的约定来控制对页面的访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: e0102ff64921a83f0330acb6f5d9bfd90f64ca7a
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994036"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="52002-103">在 ASP.NET Core razor 页授权约定</span><span class="sxs-lookup"><span data-stu-id="52002-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="52002-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52002-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52002-105">在 Razor Pages 应用程序中控制访问权限的一种方法是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="52002-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="52002-106">这些约定允许用户授权用户, 并允许匿名用户访问页面的各个页面或文件夹。</span><span class="sxs-lookup"><span data-stu-id="52002-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="52002-107">本主题中所述的约定会自动应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。</span><span class="sxs-lookup"><span data-stu-id="52002-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="52002-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="52002-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="52002-109">示例应用使用[cookie 身份验证, 但不 ASP.NET Core 标识](xref:security/authentication/cookie)。</span><span class="sxs-lookup"><span data-stu-id="52002-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="52002-110">概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。</span><span class="sxs-lookup"><span data-stu-id="52002-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="52002-111">若要使用 ASP.NET Core 标识, 请按照中<xref:security/authentication/identity>的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="52002-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="52002-112">需要授权才能访问页面</span><span class="sxs-lookup"><span data-stu-id="52002-112">Require authorization to access a page</span></span>

<span data-ttu-id="52002-113">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到页面中的指定路径: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*></span><span class="sxs-lookup"><span data-stu-id="52002-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="52002-114">指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="52002-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="52002-115">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="52002-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="52002-116">可应用于`[Authorize]`具有 filter 特性的页模型类。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter></span><span class="sxs-lookup"><span data-stu-id="52002-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="52002-117">有关详细信息, 请参阅[授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="52002-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="52002-118">需要授权才能访问页的文件夹</span><span class="sxs-lookup"><span data-stu-id="52002-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="52002-119">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*></span><span class="sxs-lookup"><span data-stu-id="52002-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="52002-120">指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="52002-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="52002-121">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="52002-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="52002-122">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="52002-122">Require authorization to access an area page</span></span>

<span data-ttu-id="52002-123">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径处的区域页: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*></span><span class="sxs-lookup"><span data-stu-id="52002-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="52002-124">页面名称是文件的路径, 该文件的扩展名相对于指定区域的页根目录。</span><span class="sxs-lookup"><span data-stu-id="52002-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="52002-125">例如, 文件*区域/标识/页面/管理/帐户*的页名称为 */Manage/Accounts*。</span><span class="sxs-lookup"><span data-stu-id="52002-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="52002-126">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="52002-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="52002-127">需要授权才能访问区域文件夹</span><span class="sxs-lookup"><span data-stu-id="52002-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="52002-128">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有区域: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*></span><span class="sxs-lookup"><span data-stu-id="52002-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="52002-129">文件夹路径是文件夹相对于指定区域的页面根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="52002-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="52002-130">例如, "*区域/标识/页面/管理/* " 下的文件的文件夹路径为 */Manage*。</span><span class="sxs-lookup"><span data-stu-id="52002-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="52002-131">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="52002-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="52002-132">允许匿名访问页面</span><span class="sxs-lookup"><span data-stu-id="52002-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="52002-133">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到位于指定路径的页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*></span><span class="sxs-lookup"><span data-stu-id="52002-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="52002-134">指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="52002-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="52002-135">允许匿名访问页面文件夹</span><span class="sxs-lookup"><span data-stu-id="52002-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="52002-136">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*></span><span class="sxs-lookup"><span data-stu-id="52002-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="52002-137">指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="52002-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="52002-138">合并授权访问和匿名访问时的注意事项</span><span class="sxs-lookup"><span data-stu-id="52002-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="52002-139">有效的方法是, 指定需要授权的页面文件夹, 并指定该文件夹中的页面允许匿名访问:</span><span class="sxs-lookup"><span data-stu-id="52002-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="52002-140">但是, 反之则无效。</span><span class="sxs-lookup"><span data-stu-id="52002-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="52002-141">不能声明用于匿名访问的页面文件夹, 然后在该文件夹中指定需要授权的页面:</span><span class="sxs-lookup"><span data-stu-id="52002-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="52002-142">在专用页面上要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="52002-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="52002-143"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>当和<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>都应用于页面时, 将<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>优先并控制访问。</span><span class="sxs-lookup"><span data-stu-id="52002-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52002-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="52002-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52002-145">在 Razor Pages 应用程序中控制访问权限的一种方法是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="52002-145">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="52002-146">这些约定允许用户授权用户, 并允许匿名用户访问页面的各个页面或文件夹。</span><span class="sxs-lookup"><span data-stu-id="52002-146">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="52002-147">本主题中所述的约定会自动应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。</span><span class="sxs-lookup"><span data-stu-id="52002-147">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="52002-148">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="52002-148">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="52002-149">示例应用使用[cookie 身份验证, 但不 ASP.NET Core 标识](xref:security/authentication/cookie)。</span><span class="sxs-lookup"><span data-stu-id="52002-149">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="52002-150">概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。</span><span class="sxs-lookup"><span data-stu-id="52002-150">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="52002-151">若要使用 ASP.NET Core 标识, 请按照中<xref:security/authentication/identity>的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="52002-151">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="52002-152">需要授权才能访问页面</span><span class="sxs-lookup"><span data-stu-id="52002-152">Require authorization to access a page</span></span>

<span data-ttu-id="52002-153">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到页面中的指定路径: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*></span><span class="sxs-lookup"><span data-stu-id="52002-153">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="52002-154">指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="52002-154">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="52002-155">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="52002-155">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="52002-156">可应用于`[Authorize]`具有 filter 特性的页模型类。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter></span><span class="sxs-lookup"><span data-stu-id="52002-156">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="52002-157">有关详细信息, 请参阅[授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="52002-157">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="52002-158">需要授权才能访问页的文件夹</span><span class="sxs-lookup"><span data-stu-id="52002-158">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="52002-159">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*></span><span class="sxs-lookup"><span data-stu-id="52002-159">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="52002-160">指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="52002-160">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="52002-161">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="52002-161">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="52002-162">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="52002-162">Require authorization to access an area page</span></span>

<span data-ttu-id="52002-163">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径处的区域页: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*></span><span class="sxs-lookup"><span data-stu-id="52002-163">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="52002-164">页面名称是文件的路径, 该文件的扩展名相对于指定区域的页根目录。</span><span class="sxs-lookup"><span data-stu-id="52002-164">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="52002-165">例如, 文件*区域/标识/页面/管理/帐户*的页名称为 */Manage/Accounts*。</span><span class="sxs-lookup"><span data-stu-id="52002-165">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="52002-166">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="52002-166">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="52002-167">需要授权才能访问区域文件夹</span><span class="sxs-lookup"><span data-stu-id="52002-167">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="52002-168">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有区域: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*></span><span class="sxs-lookup"><span data-stu-id="52002-168">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="52002-169">文件夹路径是文件夹相对于指定区域的页面根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="52002-169">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="52002-170">例如, "*区域/标识/页面/管理/* " 下的文件的文件夹路径为 */Manage*。</span><span class="sxs-lookup"><span data-stu-id="52002-170">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="52002-171">若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="52002-171">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="52002-172">允许匿名访问页面</span><span class="sxs-lookup"><span data-stu-id="52002-172">Allow anonymous access to a page</span></span>

<span data-ttu-id="52002-173">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到位于指定路径的页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*></span><span class="sxs-lookup"><span data-stu-id="52002-173">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="52002-174">指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="52002-174">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="52002-175">允许匿名访问页面文件夹</span><span class="sxs-lookup"><span data-stu-id="52002-175">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="52002-176">使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*></span><span class="sxs-lookup"><span data-stu-id="52002-176">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="52002-177">指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="52002-177">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="52002-178">合并授权访问和匿名访问时的注意事项</span><span class="sxs-lookup"><span data-stu-id="52002-178">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="52002-179">有效的方法是, 指定需要授权的页面文件夹, 并指定该文件夹中的页面允许匿名访问:</span><span class="sxs-lookup"><span data-stu-id="52002-179">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="52002-180">但是, 反之则无效。</span><span class="sxs-lookup"><span data-stu-id="52002-180">The reverse, however, isn't valid.</span></span> <span data-ttu-id="52002-181">不能声明用于匿名访问的页面文件夹, 然后在该文件夹中指定需要授权的页面:</span><span class="sxs-lookup"><span data-stu-id="52002-181">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="52002-182">在专用页面上要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="52002-182">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="52002-183"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>当和<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>都应用于页面时, 将<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>优先并控制访问。</span><span class="sxs-lookup"><span data-stu-id="52002-183">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52002-184">其他资源</span><span class="sxs-lookup"><span data-stu-id="52002-184">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
