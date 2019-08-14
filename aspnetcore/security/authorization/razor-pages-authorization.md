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
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>在 ASP.NET Core razor 页授权约定

作者：[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

在 Razor Pages 应用程序中控制访问权限的一种方法是在启动时使用授权约定。 这些约定允许用户授权用户, 并允许匿名用户访问页面的各个页面或文件夹。 本主题中所述的约定会自动应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用使用[cookie 身份验证, 但不 ASP.NET Core 标识](xref:security/authentication/cookie)。 概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。 若要使用 ASP.NET Core 标识, 请按照中<xref:security/authentication/identity>的指导进行操作。

## <a name="require-authorization-to-access-a-page"></a>需要授权才能访问页面

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到页面中的指定路径: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> 可应用于`[Authorize]`具有 filter 特性的页模型类。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 有关详细信息, 请参阅[授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>需要授权才能访问页的文件夹

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>需要授权才能访问区域页

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径处的区域页: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

页面名称是文件的路径, 该文件的扩展名相对于指定区域的页根目录。 例如, 文件*区域/标识/页面/管理/帐户*的页名称为 */Manage/Accounts*。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>需要授权才能访问区域文件夹

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有区域: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

文件夹路径是文件夹相对于指定区域的页面根目录的路径。 例如, "*区域/标识/页面/管理/* " 下的文件的文件夹路径为 */Manage*。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>允许匿名访问页面

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到位于指定路径的页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>允许匿名访问页面文件夹

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。

## <a name="note-on-combining-authorized-and-anonymous-access"></a>合并授权访问和匿名访问时的注意事项

有效的方法是, 指定需要授权的页面文件夹, 并指定该文件夹中的页面允许匿名访问:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

但是, 反之则无效。 不能声明用于匿名访问的页面文件夹, 然后在该文件夹中指定需要授权的页面:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

在专用页面上要求授权失败。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>当和<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>都应用于页面时, 将<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>优先并控制访问。

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在 Razor Pages 应用程序中控制访问权限的一种方法是在启动时使用授权约定。 这些约定允许用户授权用户, 并允许匿名用户访问页面的各个页面或文件夹。 本主题中所述的约定会自动应用[授权筛选器](xref:mvc/controllers/filters#authorization-filters)来控制访问权限。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用使用[cookie 身份验证, 但不 ASP.NET Core 标识](xref:security/authentication/cookie)。 概念和本主题中所示的示例同样适用于使用 ASP.NET Core 标识的应用。 若要使用 ASP.NET Core 标识, 请按照中<xref:security/authentication/identity>的指导进行操作。

## <a name="require-authorization-to-access-a-page"></a>需要授权才能访问页面

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到页面中的指定路径: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizePage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> 可应用于`[Authorize]`具有 filter 特性的页模型类。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 有关详细信息, 请参阅[授权筛选器属性](xref:razor-pages/filter#authorize-filter-attribute)。

## <a name="require-authorization-to-access-a-folder-of-pages"></a>需要授权才能访问页的文件夹

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>需要授权才能访问区域页

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到指定路径处的区域页: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

页面名称是文件的路径, 该文件的扩展名相对于指定区域的页根目录。 例如, 文件*区域/标识/页面/管理/帐户*的页名称为 */Manage/Accounts*。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaPage 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>需要授权才能访问区域文件夹

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>到文件夹中指定路径的所有区域: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

文件夹路径是文件夹相对于指定区域的页面根目录的路径。 例如, "*区域/标识/页面/管理/* " 下的文件的文件夹路径为 */Manage*。

若要指定[授权策略](xref:security/authorization/policies), 请使用[AuthorizeAreaFolder 重载](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>允许匿名访问页面

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到位于指定路径的页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

指定的路径为视图引擎路径, 它是不带扩展名的 Razor Pages 根相对路径, 并且仅包含正斜杠。

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>允许匿名访问页面文件夹

使用约定通过<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>将添加<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>到文件夹中指定路径的所有页面: <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

指定的路径为视图引擎路径, 该路径是 Razor Pages 根相对路径。

## <a name="note-on-combining-authorized-and-anonymous-access"></a>合并授权访问和匿名访问时的注意事项

有效的方法是, 指定需要授权的页面文件夹, 并指定该文件夹中的页面允许匿名访问:

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

但是, 反之则无效。 不能声明用于匿名访问的页面文件夹, 然后在该文件夹中指定需要授权的页面:

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

在专用页面上要求授权失败。 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>当和<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>都应用于页面时, 将<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>优先并控制访问。

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
