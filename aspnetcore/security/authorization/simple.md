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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="00888-103">ASP.NET Core 中的简单授权</span><span class="sxs-lookup"><span data-stu-id="00888-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="00888-104">MVC 中的`AuthorizeAttribute`授权通过属性及其各种参数进行控制。</span><span class="sxs-lookup"><span data-stu-id="00888-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="00888-105">最简单的情况是， `AuthorizeAttribute`将属性应用于控制器或操作会将控制器或操作的访问权限限制为任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="00888-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="00888-106">例如，以下代码将访问权限限制为任何`AccountController`经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="00888-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="00888-107">如果要对操作（而不是控制器）应用授权，请将`AuthorizeAttribute`属性应用于操作本身：</span><span class="sxs-lookup"><span data-stu-id="00888-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="00888-108">现在只有经过身份验证的用户`Logout`可以访问该函数。</span><span class="sxs-lookup"><span data-stu-id="00888-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="00888-109">你还可以使用`AllowAnonymous`属性，以允许未通过身份验证的用户访问各个操作。</span><span class="sxs-lookup"><span data-stu-id="00888-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="00888-110">例如：</span><span class="sxs-lookup"><span data-stu-id="00888-110">For example:</span></span>

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

<span data-ttu-id="00888-111">这将仅允许经过身份验证的`AccountController`用户访问，但`Login`操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。</span><span class="sxs-lookup"><span data-stu-id="00888-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="00888-112">`[AllowAnonymous]`跳过所有授权语句。</span><span class="sxs-lookup"><span data-stu-id="00888-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="00888-113">如果组合`[AllowAnonymous]`和任何`[Authorize]`属性，则`[Authorize]`忽略属性。</span><span class="sxs-lookup"><span data-stu-id="00888-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="00888-114">例如，如果在控制器`[AllowAnonymous]`级别应用，则会忽略`[Authorize]`同一控制器（或其中的任何操作）上的任何属性。</span><span class="sxs-lookup"><span data-stu-id="00888-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
