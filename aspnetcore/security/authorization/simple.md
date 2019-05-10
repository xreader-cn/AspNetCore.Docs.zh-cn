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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="48452-103">ASP.NET Core 中的简单授权</span><span class="sxs-lookup"><span data-stu-id="48452-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="48452-104">在 MVC 中的授权控制通过`AuthorizeAttribute`属性以及其各种参数。</span><span class="sxs-lookup"><span data-stu-id="48452-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="48452-105">简单地说，应用`AuthorizeAttribute`到控制器或操作的限制访问权限，到控制器或操作保存到任何经过身份验证的用户属性。</span><span class="sxs-lookup"><span data-stu-id="48452-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="48452-106">例如，下面的代码限制访问`AccountController`到任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="48452-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="48452-107">如果你想要对某个操作而不是在控制器应用授权，应用`AuthorizeAttribute`操作本身的属性：</span><span class="sxs-lookup"><span data-stu-id="48452-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="48452-108">现在，只有经过身份验证的用户可以访问`Logout`函数。</span><span class="sxs-lookup"><span data-stu-id="48452-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="48452-109">此外可以使用`AllowAnonymous`属性以允许未经身份验证的用户到单个操作的访问。</span><span class="sxs-lookup"><span data-stu-id="48452-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="48452-110">例如：</span><span class="sxs-lookup"><span data-stu-id="48452-110">For example:</span></span>

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

<span data-ttu-id="48452-111">这将允许到只有经过身份验证的用户`AccountController`，除`Login`操作，可供每个用户，而不考虑其已经过身份验证或未经身份验证 / 匿名状态。</span><span class="sxs-lookup"><span data-stu-id="48452-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="48452-112">`[AllowAnonymous]` 绕过授权的所有语句。</span><span class="sxs-lookup"><span data-stu-id="48452-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="48452-113">如果你将结合`[AllowAnonymous]`及任何`[Authorize]`属性，`[Authorize]`属性被忽略。</span><span class="sxs-lookup"><span data-stu-id="48452-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="48452-114">例如，如果将应用`[AllowAnonymous]`在控制器级别中，任何`[Authorize]`忽略属性在同一个控制器上 （或中它的任何操作）。</span><span class="sxs-lookup"><span data-stu-id="48452-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
