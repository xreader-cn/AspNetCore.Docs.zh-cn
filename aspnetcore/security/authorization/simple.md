---
title: ASP.NET Core 中的简单授权
author: rick-anderson
description: 了解如何使用授权属性限制对 ASP.NET Core 控制器和操作的访问。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775630"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core 中的简单授权

<a name="security-authorization-simple"></a>

MVC 中的`AuthorizeAttribute`授权通过属性及其各种参数进行控制。 最简单的情况是， `AuthorizeAttribute`将属性应用于控制器或操作会将控制器或操作的访问权限限制为任何经过身份验证的用户。

例如，以下代码将访问权限限制为任何`AccountController`经过身份验证的用户。

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

如果要对操作（而不是控制器）应用授权，请将`AuthorizeAttribute`属性应用于操作本身：

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

现在只有经过身份验证的用户`Logout`可以访问该函数。

你还可以使用`AllowAnonymous`属性，以允许未通过身份验证的用户访问各个操作。 例如：

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

这将仅允许经过身份验证的`AccountController`用户访问，但`Login`操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。

> [!WARNING]
> `[AllowAnonymous]`跳过所有授权语句。 如果组合`[AllowAnonymous]`和任何`[Authorize]`属性，则`[Authorize]`忽略属性。 例如，如果在控制器`[AllowAnonymous]`级别应用，则会忽略`[Authorize]`同一控制器（或其中的任何操作）上的任何属性。
