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
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>在 ASP.NET Core razor 页授权约定

作者：[Luke Latham](https://github.com/guardrex)

Razor 页面应用中控制访问权限的一种方法是在启动时使用授权约定。 这些约定，可为用户授权，并允许匿名用户访问各个页面的文件夹。 自动在本主题中所述的约定适用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用使用[cookie 身份验证，而无需 ASP.NET Core 标识](xref:security/authentication/cookie)。 概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。 若要使用 ASP.NET Core 标识，请按照中的指导<xref:security/authentication/identity>。

## <a name="require-authorization-to-access-a-page"></a>需要授权可访问的页面

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径的页面：

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定的路径是视图引擎路径，即无需扩展和包含仅正斜杠的 Razor 页面根相对路径。

若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>可以应用于具有的页面模型类`[Authorize]`筛选器特性。 有关详细信息，请参阅[Authorize 筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>需要授权才能访问页面的文件夹

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到所有指定路径处的文件夹中的页：

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定的路径是视图引擎路径，这是 Razor 页面根相对路径。

若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>需要授权才能访问区域页

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径的区域页面：

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

页面名称是文件的指定的区域不相对于页面根目录具有扩展名的路径。 例如，该文件的页名称*Areas/Identity/Pages/Manage/Accounts.cshtml*是 */管理/帐户*。

若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>需要授权才能访问区域的文件夹

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到所有指定路径处的文件夹中的区域：

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

文件夹路径是相对于指定区域的页面根目录文件夹的路径。 例如下的文件的文件夹路径*领域/Identity/页/管理/* 是 */管理*。

若要指定[授权策略](xref:security/authorization/policies)，使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>允许匿名访问的页

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到指定路径处的页面：

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定的路径是视图引擎路径，即无需扩展和包含仅正斜杠的 Razor 页面根相对路径。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>允许匿名访问的页面的文件夹

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*>通过约定<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>以添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到所有指定路径处的文件夹中的页：

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

指定的路径是视图引擎路径，这是 Razor 页面根相对路径。

## <a name="note-on-combining-authorized-and-anonymous-access"></a>请注意在组合授权和匿名访问

可以指定需要授权的页面的文件夹并不是指定该文件夹中的页面，允许匿名访问：

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

但是，与之相反，不是有效。 您不能声明为匿名访问的页面的文件夹，然后指定需要验证该文件夹中的页面：

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

在专用页的要求授权失败。 当同时<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>并<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>应用于页上，<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>优先和控制的访问。

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
