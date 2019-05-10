---
title: 在 ASP.NET Core razor 页授权约定
author: guardrex
description: 了解如何控制对页约定来授予用户权限和允许匿名用户访问页面的文件夹的访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ff061f96f30cd893b903403de760a172c924cf06
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895414"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="06d41-103">在 ASP.NET Core razor 页授权约定</span><span class="sxs-lookup"><span data-stu-id="06d41-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="06d41-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="06d41-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="06d41-105">Razor 页面应用中控制访问权限的一种方法是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="06d41-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="06d41-106">这些约定，可为用户授权，并允许匿名用户访问各个页面的文件夹。</span><span class="sxs-lookup"><span data-stu-id="06d41-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="06d41-107">自动在本主题中所述的约定适用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问。</span><span class="sxs-lookup"><span data-stu-id="06d41-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="06d41-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="06d41-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="06d41-109">示例应用使用[cookie 身份验证，而无需 ASP.NET Core 标识](xref:security/authentication/cookie)。</span><span class="sxs-lookup"><span data-stu-id="06d41-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="06d41-110">概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。</span><span class="sxs-lookup"><span data-stu-id="06d41-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="06d41-111">若要使用 ASP.NET Core 标识，请按照中的指导<xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="06d41-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="06d41-112">需要授权可访问的页面</span><span class="sxs-lookup"><span data-stu-id="06d41-112">Require authorization to access a page</span></span>

<span data-ttu-id="06d41-113">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径的页面：</span><span class="sxs-lookup"><span data-stu-id="06d41-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="06d41-114">指定的路径是视图引擎路径，即无需扩展和包含仅正斜杠的 Razor 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="06d41-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="06d41-115">若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="06d41-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="06d41-116"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>可以应用于具有的页面模型类`[Authorize]`筛选器特性。</span><span class="sxs-lookup"><span data-stu-id="06d41-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="06d41-117">有关详细信息，请参阅[Authorize 筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="06d41-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="06d41-118">需要授权才能访问页面的文件夹</span><span class="sxs-lookup"><span data-stu-id="06d41-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="06d41-119">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到所有指定路径处的文件夹中的页：</span><span class="sxs-lookup"><span data-stu-id="06d41-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="06d41-120">指定的路径是视图引擎路径，这是 Razor 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="06d41-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="06d41-121">若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="06d41-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="06d41-122">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="06d41-122">Require authorization to access an area page</span></span>

<span data-ttu-id="06d41-123">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径的区域页面：</span><span class="sxs-lookup"><span data-stu-id="06d41-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="06d41-124">页面名称是文件的指定的区域不相对于页面根目录具有扩展名的路径。</span><span class="sxs-lookup"><span data-stu-id="06d41-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="06d41-125">例如，该文件的页名称*Areas/Identity/Pages/Manage/Accounts.cshtml*是 */管理/帐户*。</span><span class="sxs-lookup"><span data-stu-id="06d41-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="06d41-126">若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="06d41-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="06d41-127">需要授权才能访问区域的文件夹</span><span class="sxs-lookup"><span data-stu-id="06d41-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="06d41-128">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到所有指定路径处的文件夹中的区域：</span><span class="sxs-lookup"><span data-stu-id="06d41-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="06d41-129">文件夹路径是相对于指定区域的页面根目录文件夹的路径。</span><span class="sxs-lookup"><span data-stu-id="06d41-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="06d41-130">例如下的文件的文件夹路径*领域/Identity/页/管理/* 是 */管理*。</span><span class="sxs-lookup"><span data-stu-id="06d41-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="06d41-131">若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="06d41-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="06d41-132">允许匿名访问的页</span><span class="sxs-lookup"><span data-stu-id="06d41-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="06d41-133">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到指定路径处的页面：</span><span class="sxs-lookup"><span data-stu-id="06d41-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="06d41-134">指定的路径是视图引擎路径，即无需扩展和包含仅正斜杠的 Razor 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="06d41-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="06d41-135">允许匿名访问的页面的文件夹</span><span class="sxs-lookup"><span data-stu-id="06d41-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="06d41-136">使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到所有指定路径处的文件夹中的页：</span><span class="sxs-lookup"><span data-stu-id="06d41-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="06d41-137">指定的路径是视图引擎路径，这是 Razor 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="06d41-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="06d41-138">请注意在组合授权和匿名访问</span><span class="sxs-lookup"><span data-stu-id="06d41-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="06d41-139">可以指定需要授权的页面的文件夹并不是指定该文件夹中的页面，允许匿名访问：</span><span class="sxs-lookup"><span data-stu-id="06d41-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="06d41-140">但是，与之相反，不是有效。</span><span class="sxs-lookup"><span data-stu-id="06d41-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="06d41-141">您不能声明为匿名访问的页面的文件夹，然后指定需要验证该文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="06d41-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="06d41-142">在专用页的要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="06d41-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="06d41-143">当同时<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>并<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>应用于页上，<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>优先和控制的访问。</span><span class="sxs-lookup"><span data-stu-id="06d41-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06d41-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="06d41-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
