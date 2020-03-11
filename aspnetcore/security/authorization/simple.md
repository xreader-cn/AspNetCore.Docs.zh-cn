---
title: ASP.NET Core 中的简单授权
author: rick-anderson
description: 了解如何使用授权属性限制对 ASP.NET Core 控制器和操作的访问。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653556"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core 中的简单授权

<a name="security-authorization-simple"></a>

MVC 中的授权通过 `AuthorizeAttribute` 属性及其各种参数进行控制。 最简单的情况是，将 `AuthorizeAttribute` 属性应用于控制器或操作会将控制器或操作的访问权限限制为任何经过身份验证的用户。

例如，以下代码将对 `AccountController` 的访问限制为任何经过身份验证的用户。

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

如果要对操作（而不是控制器）应用授权，请将 `AuthorizeAttribute` 属性应用到操作本身：

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

现在只有经过身份验证的用户才能访问 `Logout` 函数。

你还可以使用 `AllowAnonymous` 属性允许未通过身份验证的用户访问各个操作。 例如：

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

这将仅允许经过身份验证的用户访问 `AccountController`，`Login` 操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。

> [!WARNING]
> `[AllowAnonymous]` 绕过所有授权语句。 如果将 `[AllowAnonymous]` 和任何 `[Authorize]` 特性组合在一起，则将忽略 `[Authorize]` 特性。 例如，如果在控制器级别应用 `[AllowAnonymous]`，则将忽略同一控制器上的任何 `[Authorize]` 属性（或其中的任何操作）。
