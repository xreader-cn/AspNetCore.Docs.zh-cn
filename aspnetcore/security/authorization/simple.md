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
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272120"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="6959b-103">ASP.NET Core 中的简单授权</span><span class="sxs-lookup"><span data-stu-id="6959b-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="6959b-104">ASP.NET Core 中的授权通过 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 和其各种参数来控制。</span><span class="sxs-lookup"><span data-stu-id="6959b-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="6959b-105">在最简单的形式中，将 `[Authorize]` 属性应用于控制器、操作或 Razor 页面，将对该组件的访问限制为任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="6959b-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="6959b-106">例如，以下代码将访问权限限制为 `AccountController` 任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="6959b-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="6959b-107">如果要对操作（而不是控制器）应用授权，请将属性应用于 `AuthorizeAttribute` 操作本身：</span><span class="sxs-lookup"><span data-stu-id="6959b-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="6959b-108">现在只有经过身份验证的用户可以访问该 `Logout` 函数。</span><span class="sxs-lookup"><span data-stu-id="6959b-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="6959b-109">你还可以使用属性，以 `AllowAnonymous` 允许未通过身份验证的用户访问各个操作。</span><span class="sxs-lookup"><span data-stu-id="6959b-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="6959b-110">例如：</span><span class="sxs-lookup"><span data-stu-id="6959b-110">For example:</span></span>

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

<span data-ttu-id="6959b-111">这将仅允许经过身份验证的用户 `AccountController` 访问，但 `Login` 操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。</span><span class="sxs-lookup"><span data-stu-id="6959b-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="6959b-112">`[AllowAnonymous]`跳过所有授权语句。</span><span class="sxs-lookup"><span data-stu-id="6959b-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="6959b-113">如果组合 `[AllowAnonymous]` 和任何 `[Authorize]` 属性，则 `[Authorize]` 忽略属性。</span><span class="sxs-lookup"><span data-stu-id="6959b-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="6959b-114">例如，如果在 `[AllowAnonymous]` 控制器级别应用，则 `[Authorize]` 会忽略同一控制器（或其中的任何操作）上的任何属性。</span><span class="sxs-lookup"><span data-stu-id="6959b-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
