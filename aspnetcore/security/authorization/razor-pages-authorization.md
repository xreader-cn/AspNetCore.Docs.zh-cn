---
title: 在 ASP.NET Core razor 页授权约定
author: rick-anderson
description: 了解如何使用用于授权用户和允许匿名用户访问页面或文件夹的约定来控制对页面的访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 00fc487c6ac802f213bcf83994ecc2b1a1468589
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653112"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="c20ad-103">在 ASP.NET Core razor 页授权约定</span><span class="sxs-lookup"><span data-stu-id="c20ad-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c20ad-104">在 Razor Pages 应用程序中控制访问权限的一种方法是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="c20ad-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="c20ad-105">这些约定允许用户授权用户，并允许匿名用户访问页面的各个页面或文件夹。</span><span class="sxs-lookup"><span data-stu-id="c20ad-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="c20ad-106">本主题中所述的约定会自动应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。</span><span class="sxs-lookup"><span data-stu-id="c20ad-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="c20ad-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c20ad-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c20ad-108">示例应用使用[cookie 身份验证，但不 ASP.NET Core 标识](xref:security/authentication/cookie)。</span><span class="sxs-lookup"><span data-stu-id="c20ad-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="c20ad-109">概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。</span><span class="sxs-lookup"><span data-stu-id="c20ad-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="c20ad-110">若要使用 ASP.NET Core 标识，请按照 <xref:security/authentication/identity>中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="c20ad-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="c20ad-111">需要授权才能访问页面</span><span class="sxs-lookup"><span data-stu-id="c20ad-111">Require authorization to access a page</span></span>

<span data-ttu-id="c20ad-112">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到页面中的指定路径：</span><span class="sxs-lookup"><span data-stu-id="c20ad-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="c20ad-113">指定的路径为视图引擎路径，它是不带扩展名的 Razor Pages 根相对路径，并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="c20ad-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="c20ad-114">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="c20ad-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 可以应用于具有 `[Authorize]` filter 特性的页模型类。</span><span class="sxs-lookup"><span data-stu-id="c20ad-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="c20ad-116">有关详细信息，请参阅[授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="c20ad-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="c20ad-117">需要授权才能访问页的文件夹</span><span class="sxs-lookup"><span data-stu-id="c20ad-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="c20ad-118">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="c20ad-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="c20ad-119">指定的路径为视图引擎路径，该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="c20ad-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="c20ad-120">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="c20ad-121">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="c20ad-121">Require authorization to access an area page</span></span>

<span data-ttu-id="c20ad-122">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到指定路径处的区域页：</span><span class="sxs-lookup"><span data-stu-id="c20ad-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="c20ad-123">页面名称是文件的路径，该文件的扩展名相对于指定区域的页根目录。</span><span class="sxs-lookup"><span data-stu-id="c20ad-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c20ad-124">例如，文件*区域/标识/页面/管理/帐户*的页名称为 */Manage/Accounts*。</span><span class="sxs-lookup"><span data-stu-id="c20ad-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="c20ad-125">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="c20ad-126">需要授权才能访问区域文件夹</span><span class="sxs-lookup"><span data-stu-id="c20ad-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="c20ad-127">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到文件夹中指定路径的所有区域：</span><span class="sxs-lookup"><span data-stu-id="c20ad-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="c20ad-128">文件夹路径是文件夹相对于指定区域的页面根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="c20ad-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c20ad-129">例如，"*区域/标识/页面/管理/* " 下的文件的文件夹路径为 */Manage*。</span><span class="sxs-lookup"><span data-stu-id="c20ad-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="c20ad-130">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="c20ad-131">允许匿名访问页面</span><span class="sxs-lookup"><span data-stu-id="c20ad-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="c20ad-132">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 添加到页面中的指定路径：</span><span class="sxs-lookup"><span data-stu-id="c20ad-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="c20ad-133">指定的路径为视图引擎路径，它是不带扩展名的 Razor Pages 根相对路径，并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="c20ad-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="c20ad-134">允许匿名访问页面文件夹</span><span class="sxs-lookup"><span data-stu-id="c20ad-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="c20ad-135">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 添加到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="c20ad-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="c20ad-136">指定的路径为视图引擎路径，该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="c20ad-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="c20ad-137">合并授权访问和匿名访问时的注意事项</span><span class="sxs-lookup"><span data-stu-id="c20ad-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="c20ad-138">有效的方法是指定页面的文件夹需要授权，然后指定该文件夹中的页面允许匿名访问：</span><span class="sxs-lookup"><span data-stu-id="c20ad-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="c20ad-139">但是，反之则无效。</span><span class="sxs-lookup"><span data-stu-id="c20ad-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="c20ad-140">不能声明用于匿名访问的页面文件夹，然后在该文件夹中指定需要授权的页面：</span><span class="sxs-lookup"><span data-stu-id="c20ad-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="c20ad-141">在专用页面上要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="c20ad-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="c20ad-142">将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 应用于页面时，<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 优先并控制访问。</span><span class="sxs-lookup"><span data-stu-id="c20ad-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c20ad-143">其他资源</span><span class="sxs-lookup"><span data-stu-id="c20ad-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c20ad-144">在 Razor Pages 应用程序中控制访问权限的一种方法是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="c20ad-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="c20ad-145">这些约定允许用户授权用户，并允许匿名用户访问页面的各个页面或文件夹。</span><span class="sxs-lookup"><span data-stu-id="c20ad-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="c20ad-146">本主题中所述的约定会自动应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。</span><span class="sxs-lookup"><span data-stu-id="c20ad-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="c20ad-147">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c20ad-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c20ad-148">示例应用使用[cookie 身份验证，但不 ASP.NET Core 标识](xref:security/authentication/cookie)。</span><span class="sxs-lookup"><span data-stu-id="c20ad-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="c20ad-149">概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。</span><span class="sxs-lookup"><span data-stu-id="c20ad-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="c20ad-150">若要使用 ASP.NET Core 标识，请按照 <xref:security/authentication/identity>中的指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="c20ad-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="c20ad-151">需要授权才能访问页面</span><span class="sxs-lookup"><span data-stu-id="c20ad-151">Require authorization to access a page</span></span>

<span data-ttu-id="c20ad-152">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到页面中的指定路径：</span><span class="sxs-lookup"><span data-stu-id="c20ad-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="c20ad-153">指定的路径为视图引擎路径，它是不带扩展名的 Razor Pages 根相对路径，并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="c20ad-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="c20ad-154">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="c20ad-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 可以应用于具有 `[Authorize]` filter 特性的页模型类。</span><span class="sxs-lookup"><span data-stu-id="c20ad-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="c20ad-156">有关详细信息，请参阅[授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="c20ad-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="c20ad-157">需要授权才能访问页的文件夹</span><span class="sxs-lookup"><span data-stu-id="c20ad-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="c20ad-158">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="c20ad-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="c20ad-159">指定的路径为视图引擎路径，该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="c20ad-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="c20ad-160">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="c20ad-161">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="c20ad-161">Require authorization to access an area page</span></span>

<span data-ttu-id="c20ad-162">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到指定路径处的区域页：</span><span class="sxs-lookup"><span data-stu-id="c20ad-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="c20ad-163">页面名称是文件的路径，该文件的扩展名相对于指定区域的页根目录。</span><span class="sxs-lookup"><span data-stu-id="c20ad-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c20ad-164">例如，文件*区域/标识/页面/管理/帐户*的页名称为 */Manage/Accounts*。</span><span class="sxs-lookup"><span data-stu-id="c20ad-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="c20ad-165">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="c20ad-166">需要授权才能访问区域文件夹</span><span class="sxs-lookup"><span data-stu-id="c20ad-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="c20ad-167">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 添加到文件夹中指定路径的所有区域：</span><span class="sxs-lookup"><span data-stu-id="c20ad-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="c20ad-168">文件夹路径是文件夹相对于指定区域的页面根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="c20ad-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="c20ad-169">例如，"*区域/标识/页面/管理/* " 下的文件的文件夹路径为 */Manage*。</span><span class="sxs-lookup"><span data-stu-id="c20ad-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="c20ad-170">若要指定[授权策略](xref:security/authorization/policies)，请使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)：</span><span class="sxs-lookup"><span data-stu-id="c20ad-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="c20ad-171">允许匿名访问页面</span><span class="sxs-lookup"><span data-stu-id="c20ad-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="c20ad-172">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 添加到页面中的指定路径：</span><span class="sxs-lookup"><span data-stu-id="c20ad-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="c20ad-173">指定的路径为视图引擎路径，它是不带扩展名的 Razor Pages 根相对路径，并且仅包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="c20ad-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="c20ad-174">允许匿名访问页面文件夹</span><span class="sxs-lookup"><span data-stu-id="c20ad-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="c20ad-175">通过 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 约定，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 添加到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="c20ad-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="c20ad-176">指定的路径为视图引擎路径，该路径是 Razor Pages 根相对路径。</span><span class="sxs-lookup"><span data-stu-id="c20ad-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="c20ad-177">合并授权访问和匿名访问时的注意事项</span><span class="sxs-lookup"><span data-stu-id="c20ad-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="c20ad-178">有效的方法是，指定需要授权的页面文件夹，并指定该文件夹中的页面允许匿名访问：</span><span class="sxs-lookup"><span data-stu-id="c20ad-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="c20ad-179">但是，反之则无效。</span><span class="sxs-lookup"><span data-stu-id="c20ad-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="c20ad-180">不能声明用于匿名访问的页面文件夹，然后在该文件夹中指定需要授权的页面：</span><span class="sxs-lookup"><span data-stu-id="c20ad-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="c20ad-181">在专用页面上要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="c20ad-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="c20ad-182">将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 应用于页面时，<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 优先并控制访问。</span><span class="sxs-lookup"><span data-stu-id="c20ad-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c20ad-183">其他资源</span><span class="sxs-lookup"><span data-stu-id="c20ad-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
