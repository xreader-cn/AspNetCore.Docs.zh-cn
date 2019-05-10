---
title: ASP.NET Core mvc 视图基于授权
author: rick-anderson
description: 本文档演示如何将注入和利用 ASP.NET Core Razor 视图内的授权服务。
ms.author: riande
ms.date: 10/30/2017
uid: security/authorization/views
ms.openlocfilehash: e497c41d4dca29fed8733f18cf727804e3f06d8c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892054"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core mvc 视图基于授权

开发人员通常想要显示、 隐藏或以其他方式修改基于当前用户标识的用户界面。 您可以访问通过 MVC 视图中的授权服务[依赖关系注入](xref:fundamentals/dependency-injection)。 若要将授权服务注入到 Razor 视图中，使用`@inject`指令：

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

如果希望每个视图中的授权服务，将放置`@inject`指令插入 *_ViewImports.cshtml*的文件*视图*目录。 有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。

使用注入的授权服务调用`AuthorizeAsync`中完全相同的方式会检查期间[基于资源的授权](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

在某些情况下，该资源将视图模型。 调用`AuthorizeAsync`中完全相同的方式会检查期间[基于资源的授权](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

在前面的代码中，模型应采取的策略评估的资源作为传递纳入考虑范围。

> [!WARNING]
> 不要依赖于作为唯一的授权检查的应用程序的 UI 元素的切换可见性。 隐藏 UI 元素可能无法完全阻止访问到其关联的控制器操作。 例如，考虑前面的代码段中的按钮。 用户可以调用`Edit`操作方法，如果他或她知道相对资源 URL 是 */Document/Edit/1*。 出于此原因，`Edit`操作方法应执行其自己的授权检查。
