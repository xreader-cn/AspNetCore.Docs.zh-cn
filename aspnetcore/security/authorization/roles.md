---
title: ASP.NET Core 中基于角色的授权
author: rick-anderson
description: 了解如何通过将角色传递给 Authorize 属性限制 ASP.NET Core 控制器和操作访问。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 0467ea82831bffe6882e584930c2fa1212a244c7
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248090"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="c2165-103">ASP.NET Core 中基于角色的授权</span><span class="sxs-lookup"><span data-stu-id="c2165-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="c2165-104">创建一个标识时它可能属于一个或多个角色。</span><span class="sxs-lookup"><span data-stu-id="c2165-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="c2165-105">例如，爱妻 Tracy 可能属于管理员和用户角色，同时 Scott 可能仅属于用户角色。</span><span class="sxs-lookup"><span data-stu-id="c2165-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="c2165-106">如何创建和管理这些角色取决于后备存储的授权过程。</span><span class="sxs-lookup"><span data-stu-id="c2165-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="c2165-107">角色公开为通过开发人员[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)方法[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)类。</span><span class="sxs-lookup"><span data-stu-id="c2165-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

::: moniker range=">= aspnetcore-2.0"

> [!IMPORTANT]
> <span data-ttu-id="c2165-108">本主题不适用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="c2165-108">This topic does **not** apply to Razor Pages.</span></span> <span data-ttu-id="c2165-109">Razor 页面支持[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter)并[iasyncpagefilter 表示](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter)。</span><span class="sxs-lookup"><span data-stu-id="c2165-109">Razor Pages supports [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span></span> <span data-ttu-id="c2165-110">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="c2165-110">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

::: moniker-end

## <a name="adding-role-checks"></a><span data-ttu-id="c2165-111">添加角色检查</span><span class="sxs-lookup"><span data-stu-id="c2165-111">Adding role checks</span></span>

<span data-ttu-id="c2165-112">基于角色的授权检查是声明性&mdash;开发人员将其嵌入在其代码中，对控制器或在控制器内的动作指定当前用户必须是成员的访问请求的资源的角色。</span><span class="sxs-lookup"><span data-stu-id="c2165-112">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="c2165-113">例如，下面的代码上限制任何操作的访问权限`AdministrationController`谁是其成员的用户到`Administrator`角色：</span><span class="sxs-lookup"><span data-stu-id="c2165-113">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="c2165-114">以逗号分隔的列表，可以指定多个角色：</span><span class="sxs-lookup"><span data-stu-id="c2165-114">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="c2165-115">将仅可由成员的用户访问此控制器的`HRManager`角色或`Finance`角色。</span><span class="sxs-lookup"><span data-stu-id="c2165-115">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="c2165-116">如果在应用多个属性，则访问用户必须是指定; 的所有角色的成员下面的示例要求用户必须是两个成员`PowerUser`和`ControlPanelUser`角色。</span><span class="sxs-lookup"><span data-stu-id="c2165-116">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="c2165-117">通过应用其他角色授权属性在操作级别，可以进一步限制访问权限：</span><span class="sxs-lookup"><span data-stu-id="c2165-117">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="c2165-118">中的上一代码片段成员`Administrator`角色或`PowerUser`角色可访问该控制器并`SetTime`操作，但只有的成员`Administrator`角色可以访问`ShutDown`操作。</span><span class="sxs-lookup"><span data-stu-id="c2165-118">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="c2165-119">您还可以锁定一个控制器，但允许匿名、 未经身份验证访问各项操作。</span><span class="sxs-lookup"><span data-stu-id="c2165-119">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="c2165-120">基于策略角色检查</span><span class="sxs-lookup"><span data-stu-id="c2165-120">Policy based role checks</span></span>

<span data-ttu-id="c2165-121">此外可以使用新的策略语法，其中一名开发人员将策略在启动时注册为授权服务配置的一部分表示角色的要求。</span><span class="sxs-lookup"><span data-stu-id="c2165-121">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="c2165-122">这通常发生在`ConfigureServices()`在您*Startup.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="c2165-122">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```

<span data-ttu-id="c2165-123">使用应用策略`Policy`属性上的`AuthorizeAttribute`属性：</span><span class="sxs-lookup"><span data-stu-id="c2165-123">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="c2165-124">如果你想要指定多个允许的角色中一项要求，则您可以将他们指定为参数`RequireRole`方法：</span><span class="sxs-lookup"><span data-stu-id="c2165-124">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="c2165-125">此示例中授权用户属于`Administrator`，`PowerUser`或`BackupAdministrator`角色。</span><span class="sxs-lookup"><span data-stu-id="c2165-125">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="c2165-126">将角色服务添加到标识</span><span class="sxs-lookup"><span data-stu-id="c2165-126">Add Role services to Identity</span></span>

<span data-ttu-id="c2165-127">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="c2165-127">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]