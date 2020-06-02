---
<span data-ttu-id="58511-101">标题： ASP.NET Core 作者的简单授权： anderson 说明：了解如何使用授权属性限制对 ASP.NET Core 控制器和操作的访问。</span><span class="sxs-lookup"><span data-stu-id="58511-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="58511-102">ms-chap： riande：10/14/2016 无位置：</span><span class="sxs-lookup"><span data-stu-id="58511-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="58511-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="58511-103">'Blazor'</span></span>
- <span data-ttu-id="58511-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="58511-104">'Identity'</span></span>
- <span data-ttu-id="58511-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="58511-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="58511-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="58511-106">'Razor'</span></span>
- <span data-ttu-id="58511-107">" SignalR " uid：安全性/授权/简单</span><span class="sxs-lookup"><span data-stu-id="58511-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="58511-108">ASP.NET Core 中的简单授权</span><span class="sxs-lookup"><span data-stu-id="58511-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="58511-109">ASP.NET Core 中的授权通过 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 和其各种参数来控制。</span><span class="sxs-lookup"><span data-stu-id="58511-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="58511-110">在最简单的形式中，将 `[Authorize]` 属性应用于控制器、操作或 Razor 页面，将对该组件的访问限制为任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="58511-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="58511-111">例如，以下代码将访问权限限制为 `AccountController` 任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="58511-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="58511-112">如果要对操作（而不是控制器）应用授权，请将属性应用于 `AuthorizeAttribute` 操作本身：</span><span class="sxs-lookup"><span data-stu-id="58511-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="58511-113">现在只有经过身份验证的用户可以访问该 `Logout` 函数。</span><span class="sxs-lookup"><span data-stu-id="58511-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="58511-114">你还可以使用属性，以 `AllowAnonymous` 允许未通过身份验证的用户访问各个操作。</span><span class="sxs-lookup"><span data-stu-id="58511-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="58511-115">例如：</span><span class="sxs-lookup"><span data-stu-id="58511-115">For example:</span></span>

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

<span data-ttu-id="58511-116">这将仅允许经过身份验证的用户 `AccountController` 访问，但 `Login` 操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。</span><span class="sxs-lookup"><span data-stu-id="58511-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="58511-117">`[AllowAnonymous]`跳过所有授权语句。</span><span class="sxs-lookup"><span data-stu-id="58511-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="58511-118">如果组合 `[AllowAnonymous]` 和任何 `[Authorize]` 属性，则 `[Authorize]` 忽略属性。</span><span class="sxs-lookup"><span data-stu-id="58511-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="58511-119">例如，如果在 `[AllowAnonymous]` 控制器级别应用，则 `[Authorize]` 会忽略同一控制器（或其中的任何操作）上的任何属性。</span><span class="sxs-lookup"><span data-stu-id="58511-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
