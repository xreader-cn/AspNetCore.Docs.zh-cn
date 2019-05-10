---
title: ASP.NET Core 中的简单授权
author: rick-anderson
description: 了解如何使用 Authorize 属性来限制对 ASP.NET Core 控制器和操作的访问。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897674"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core 中的简单授权

<a name="security-authorization-simple"></a>

在 MVC 中的授权控制通过`AuthorizeAttribute`属性以及其各种参数。 简单地说，应用`AuthorizeAttribute`到控制器或操作的限制访问权限，到控制器或操作保存到任何经过身份验证的用户属性。

例如，下面的代码限制访问`AccountController`到任何经过身份验证的用户。

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

如果你想要对某个操作而不是在控制器应用授权，应用`AuthorizeAttribute`操作本身的属性：

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

现在，只有经过身份验证的用户可以访问`Logout`函数。

此外可以使用`AllowAnonymous`属性以允许未经身份验证的用户到单个操作的访问。 例如：

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

这将允许到只有经过身份验证的用户`AccountController`，除`Login`操作，可供每个用户，而不考虑其已经过身份验证或未经身份验证 / 匿名状态。

> [!WARNING]
> `[AllowAnonymous]` 绕过授权的所有语句。 如果你将结合`[AllowAnonymous]`及任何`[Authorize]`属性，`[Authorize]`属性被忽略。 例如，如果将应用`[AllowAnonymous]`在控制器级别中，任何`[Authorize]`忽略属性在同一个控制器上 （或中它的任何操作）。
