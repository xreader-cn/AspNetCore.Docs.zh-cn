---
title: ':::no-loc(Razor)::: 页面中的授权约定 ASP.NET Core'
author: rick-anderson
description: 了解如何使用用于授权用户和允许匿名用户访问页面或文件夹的约定来控制对页面的访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060178"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="f282c-103">:::no-loc(Razor)::: 页面中的授权约定 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f282c-103">:::no-loc(Razor)::: Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f282c-104">在页面应用中控制访问权限的一种方法 :::no-loc(Razor)::: 是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="f282c-104">One way to control access in your :::no-loc(Razor)::: Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="f282c-105">这些约定允许用户授权用户，并允许匿名用户访问页面的各个页面或文件夹。</span><span class="sxs-lookup"><span data-stu-id="f282c-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="f282c-106">本主题中所述的约定会自动应用 [授权筛选器](xref:mvc/controllers/filters#authorization-filters) 来控制访问权限。</span><span class="sxs-lookup"><span data-stu-id="f282c-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="f282c-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f282c-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f282c-108">示例应用[ :::no-loc(cookie)::: :::no-loc(ASP.NET Core Identity)::: 不使用身份验证](xref:security/authentication/:::no-loc(cookie):::)。</span><span class="sxs-lookup"><span data-stu-id="f282c-108">The sample app uses [:::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::](xref:security/authentication/:::no-loc(cookie):::).</span></span> <span data-ttu-id="f282c-109">本主题中所示的概念和示例同样适用于使用的应用 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="f282c-109">The concepts and examples shown in this topic apply equally to apps that use :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="f282c-110">若要使用 :::no-loc(ASP.NET Core Identity)::: ，请按照中的指导进行操作 <xref:security/authentication/identity> 。</span><span class="sxs-lookup"><span data-stu-id="f282c-110">To use :::no-loc(ASP.NET Core Identity):::, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="f282c-111">需要授权才能访问页面</span><span class="sxs-lookup"><span data-stu-id="f282c-111">Require authorization to access a page</span></span>

<span data-ttu-id="f282c-112">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 约定将添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到页面中的指定路径：</span><span class="sxs-lookup"><span data-stu-id="f282c-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="f282c-113">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径，无扩展名，只包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="f282c-113">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="f282c-114">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="f282c-115"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>可应用于具有 filter 特性的页模型类 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="f282c-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="f282c-116">有关详细信息，请参阅 [授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="f282c-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="f282c-117">需要授权才能访问页的文件夹</span><span class="sxs-lookup"><span data-stu-id="f282c-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="f282c-118">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 约定将添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="f282c-119">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="f282c-119">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

<span data-ttu-id="f282c-120">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="f282c-121">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="f282c-121">Require authorization to access an area page</span></span>

<span data-ttu-id="f282c-122">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 约定将添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到指定路径处的区域页：</span><span class="sxs-lookup"><span data-stu-id="f282c-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts");
```

<span data-ttu-id="f282c-123">页面名称是文件的路径，该文件的扩展名相对于指定区域的页根目录。</span><span class="sxs-lookup"><span data-stu-id="f282c-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f282c-124">例如，文件 *区域/ :::no-loc(Identity)::: /Pages/Manage/Accounts.cshtml* 的页面名称为 */Manage/Accounts* 。</span><span class="sxs-lookup"><span data-stu-id="f282c-124">For example, the page name for the file *Areas/:::no-loc(Identity):::/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="f282c-125">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="f282c-126">需要授权才能访问区域文件夹</span><span class="sxs-lookup"><span data-stu-id="f282c-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="f282c-127">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 约定将添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到文件夹中指定路径的所有区域：</span><span class="sxs-lookup"><span data-stu-id="f282c-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage");
```

<span data-ttu-id="f282c-128">文件夹路径是文件夹相对于指定区域的页面根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="f282c-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f282c-129">例如， *区域/ :::no-loc(Identity)::: /Pages/Manage/* 下的文件的文件夹路径为 */Manage* 。</span><span class="sxs-lookup"><span data-stu-id="f282c-129">For example, the folder path for the files under *Areas/:::no-loc(Identity):::/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="f282c-130">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="f282c-131">允许匿名访问页面</span><span class="sxs-lookup"><span data-stu-id="f282c-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="f282c-132">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 约定将添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 到位于指定路径的页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="f282c-133">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径，无扩展名，只包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="f282c-133">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="f282c-134">允许匿名访问页面文件夹</span><span class="sxs-lookup"><span data-stu-id="f282c-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="f282c-135">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 约定将添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f282c-136">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="f282c-136">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="f282c-137">合并授权访问和匿名访问时的注意事项</span><span class="sxs-lookup"><span data-stu-id="f282c-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="f282c-138">有效的方法是指定页面的文件夹需要授权，然后指定该文件夹中的页面允许匿名访问：</span><span class="sxs-lookup"><span data-stu-id="f282c-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="f282c-139">但是，反之则无效。</span><span class="sxs-lookup"><span data-stu-id="f282c-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="f282c-140">不能声明用于匿名访问的页面文件夹，然后在该文件夹中指定需要授权的页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="f282c-141">在专用页面上要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="f282c-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="f282c-142">当 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 都应用于页面时，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 优先并控制访问。</span><span class="sxs-lookup"><span data-stu-id="f282c-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f282c-143">其他资源</span><span class="sxs-lookup"><span data-stu-id="f282c-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f282c-144">在页面应用中控制访问权限的一种方法 :::no-loc(Razor)::: 是在启动时使用授权约定。</span><span class="sxs-lookup"><span data-stu-id="f282c-144">One way to control access in your :::no-loc(Razor)::: Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="f282c-145">这些约定允许用户授权用户，并允许匿名用户访问页面的各个页面或文件夹。</span><span class="sxs-lookup"><span data-stu-id="f282c-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="f282c-146">本主题中所述的约定会自动应用 [授权筛选器](xref:mvc/controllers/filters#authorization-filters) 来控制访问权限。</span><span class="sxs-lookup"><span data-stu-id="f282c-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="f282c-147">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f282c-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f282c-148">示例应用[ :::no-loc(cookie)::: :::no-loc(ASP.NET Core Identity)::: 不使用身份验证](xref:security/authentication/:::no-loc(cookie):::)。</span><span class="sxs-lookup"><span data-stu-id="f282c-148">The sample app uses [:::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::](xref:security/authentication/:::no-loc(cookie):::).</span></span> <span data-ttu-id="f282c-149">本主题中所示的概念和示例同样适用于使用的应用 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="f282c-149">The concepts and examples shown in this topic apply equally to apps that use :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="f282c-150">若要使用 :::no-loc(ASP.NET Core Identity)::: ，请按照中的指导进行操作 <xref:security/authentication/identity> 。</span><span class="sxs-lookup"><span data-stu-id="f282c-150">To use :::no-loc(ASP.NET Core Identity):::, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="f282c-151">需要授权才能访问页面</span><span class="sxs-lookup"><span data-stu-id="f282c-151">Require authorization to access a page</span></span>

<span data-ttu-id="f282c-152">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 约定通过将 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到页面中的指定路径：</span><span class="sxs-lookup"><span data-stu-id="f282c-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="f282c-153">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径，无扩展名，只包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="f282c-153">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="f282c-154">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="f282c-155"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>可应用于具有 filter 特性的页模型类 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="f282c-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="f282c-156">有关详细信息，请参阅 [授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。</span><span class="sxs-lookup"><span data-stu-id="f282c-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="f282c-157">需要授权才能访问页的文件夹</span><span class="sxs-lookup"><span data-stu-id="f282c-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="f282c-158">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 约定通过将 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="f282c-159">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="f282c-159">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

<span data-ttu-id="f282c-160">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="f282c-161">需要授权才能访问区域页</span><span class="sxs-lookup"><span data-stu-id="f282c-161">Require authorization to access an area page</span></span>

<span data-ttu-id="f282c-162">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 约定通过将 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到指定路径处的区域页：</span><span class="sxs-lookup"><span data-stu-id="f282c-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts");
```

<span data-ttu-id="f282c-163">页面名称是文件的路径，该文件的扩展名相对于指定区域的页根目录。</span><span class="sxs-lookup"><span data-stu-id="f282c-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f282c-164">例如，文件 *区域/ :::no-loc(Identity)::: /Pages/Manage/Accounts.cshtml* 的页面名称为 */Manage/Accounts* 。</span><span class="sxs-lookup"><span data-stu-id="f282c-164">For example, the page name for the file *Areas/:::no-loc(Identity):::/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="f282c-165">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="f282c-166">需要授权才能访问区域文件夹</span><span class="sxs-lookup"><span data-stu-id="f282c-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="f282c-167">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 约定通过将 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 到文件夹中指定路径的所有区域：</span><span class="sxs-lookup"><span data-stu-id="f282c-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage");
```

<span data-ttu-id="f282c-168">文件夹路径是文件夹相对于指定区域的页面根目录的路径。</span><span class="sxs-lookup"><span data-stu-id="f282c-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="f282c-169">例如， *区域/ :::no-loc(Identity)::: /Pages/Manage/* 下的文件的文件夹路径为 */Manage* 。</span><span class="sxs-lookup"><span data-stu-id="f282c-169">For example, the folder path for the files under *Areas/:::no-loc(Identity):::/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="f282c-170">若要指定 [授权策略](xref:security/authorization/policies)，请使用 [AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)：</span><span class="sxs-lookup"><span data-stu-id="f282c-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="f282c-171">允许匿名访问页面</span><span class="sxs-lookup"><span data-stu-id="f282c-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="f282c-172">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 约定通过将 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 到位于指定路径的页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="f282c-173">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径，无扩展名，只包含正斜杠。</span><span class="sxs-lookup"><span data-stu-id="f282c-173">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="f282c-174">允许匿名访问页面文件夹</span><span class="sxs-lookup"><span data-stu-id="f282c-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="f282c-175">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 约定通过将 <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> 添加 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 到文件夹中指定路径的所有页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="f282c-176">指定的路径是视图引擎路径，它是 :::no-loc(Razor)::: 页面根相对路径。</span><span class="sxs-lookup"><span data-stu-id="f282c-176">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="f282c-177">合并授权访问和匿名访问时的注意事项</span><span class="sxs-lookup"><span data-stu-id="f282c-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="f282c-178">有效的方法是，指定需要授权的页面文件夹，并指定该文件夹中的页面允许匿名访问：</span><span class="sxs-lookup"><span data-stu-id="f282c-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="f282c-179">但是，反之则无效。</span><span class="sxs-lookup"><span data-stu-id="f282c-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="f282c-180">不能声明用于匿名访问的页面文件夹，然后在该文件夹中指定需要授权的页面：</span><span class="sxs-lookup"><span data-stu-id="f282c-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="f282c-181">在专用页面上要求授权失败。</span><span class="sxs-lookup"><span data-stu-id="f282c-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="f282c-182">当 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 都应用于页面时，将 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 优先并控制访问。</span><span class="sxs-lookup"><span data-stu-id="f282c-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f282c-183">其他资源</span><span class="sxs-lookup"><span data-stu-id="f282c-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
