---
标题： ASP.NET Core 作者的简单授权： anderson 说明：了解如何使用授权属性限制对 ASP.NET Core 控制器和操作的访问。
ms-chap： riande：10/14/2016 无位置：
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- " SignalR " uid：安全性/授权/简单

---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core 中的简单授权

<a name="security-authorization-simple"></a>

ASP.NET Core 中的授权通过 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 和其各种参数来控制。 在最简单的形式中，将 `[Authorize]` 属性应用于控制器、操作或 Razor 页面，将对该组件的访问限制为任何经过身份验证的用户。

例如，以下代码将访问权限限制为 `AccountController` 任何经过身份验证的用户。

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

如果要对操作（而不是控制器）应用授权，请将属性应用于 `AuthorizeAttribute` 操作本身：

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

现在只有经过身份验证的用户可以访问该 `Logout` 函数。

你还可以使用属性，以 `AllowAnonymous` 允许未通过身份验证的用户访问各个操作。 例如：

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

这将仅允许经过身份验证的用户 `AccountController` 访问，但 `Login` 操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。

> [!WARNING]
> `[AllowAnonymous]`跳过所有授权语句。 如果组合 `[AllowAnonymous]` 和任何 `[Authorize]` 属性，则 `[Authorize]` 忽略属性。 例如，如果在 `[AllowAnonymous]` 控制器级别应用，则 `[Authorize]` 会忽略同一控制器（或其中的任何操作）上的任何属性。
