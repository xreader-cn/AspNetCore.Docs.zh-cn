---
title: ASP.NET Core 中的基于角色的授权
author: rick-anderson
description: 了解如何通过将角色传递到授权属性来限制 ASP.NET Core 控制器和操作访问。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051156"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="f4105-103">ASP.NET Core 中的基于角色的授权</span><span class="sxs-lookup"><span data-stu-id="f4105-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="f4105-104">创建标识时，它可能属于一个或多个角色。</span><span class="sxs-lookup"><span data-stu-id="f4105-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="f4105-105">例如，Tracy 可能属于管理员角色和用户角色，但 Scott 可能只属于用户角色。</span><span class="sxs-lookup"><span data-stu-id="f4105-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="f4105-106">如何创建和管理这些角色取决于授权过程的后备存储。</span><span class="sxs-lookup"><span data-stu-id="f4105-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="f4105-107">角色通过[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)类的[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)方法向开发人员公开。</span><span class="sxs-lookup"><span data-stu-id="f4105-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="f4105-108">添加角色检查</span><span class="sxs-lookup"><span data-stu-id="f4105-108">Adding role checks</span></span>

<span data-ttu-id="f4105-109">基于角色的授权检查是声明性 &mdash; 的，开发人员将其嵌入到代码中、控制器或控制器内的操作，指定当前用户必须是其成员的角色才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="f4105-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="f4105-110">例如，以下代码将访问权限限制为属于角色成员的用户的任何操作 `AdministrationController` `Administrator` ：</span><span class="sxs-lookup"><span data-stu-id="f4105-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="f4105-111">可以将多个角色指定为逗号分隔列表：</span><span class="sxs-lookup"><span data-stu-id="f4105-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="f4105-112">此控制器仅可供作为角色或角色成员的用户访问 `HRManager` `Finance` 。</span><span class="sxs-lookup"><span data-stu-id="f4105-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="f4105-113">如果应用多个属性，则访问用户必须是所有指定角色的成员;下面的示例要求用户必须是 `PowerUser` 和角色的成员 `ControlPanelUser` 。</span><span class="sxs-lookup"><span data-stu-id="f4105-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="f4105-114">您可以通过在操作级别应用其他角色授权属性来进一步限制访问权限：</span><span class="sxs-lookup"><span data-stu-id="f4105-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="f4105-115">在前面的代码片段中 `Administrator` ，角色或角色的成员 `PowerUser` 可以访问控制器和 `SetTime` 操作，但只有角色的成员 `Administrator` 才能访问该 `ShutDown` 操作。</span><span class="sxs-lookup"><span data-stu-id="f4105-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="f4105-116">你还可以锁定控制器，但允许对单个操作进行匿名、未经身份验证的访问。</span><span class="sxs-lookup"><span data-stu-id="f4105-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f4105-117">对于 :::no-loc(Razor)::: 页面， `AuthorizeAttribute` 可以通过以下任一方法来应用：</span><span class="sxs-lookup"><span data-stu-id="f4105-117">For :::no-loc(Razor)::: Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="f4105-118">使用 [约定](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)，或</span><span class="sxs-lookup"><span data-stu-id="f4105-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="f4105-119">将应用 `AuthorizeAttribute` 到 `PageModel` 实例：</span><span class="sxs-lookup"><span data-stu-id="f4105-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="f4105-120">筛选器属性（包括 `AuthorizeAttribute` ）只能应用于 PageModel，不能应用于特定页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="f4105-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="f4105-121">基于策略的角色检查</span><span class="sxs-lookup"><span data-stu-id="f4105-121">Policy based role checks</span></span>

<span data-ttu-id="f4105-122">还可以使用新策略语法来表示角色要求，开发人员可在其中将在启动时作为授权服务配置的一部分注册策略。</span><span class="sxs-lookup"><span data-stu-id="f4105-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="f4105-123">这通常出现在 `ConfigureServices()` *Startup.cs* 文件中。</span><span class="sxs-lookup"><span data-stu-id="f4105-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
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
::: moniker-end

<span data-ttu-id="f4105-124">策略是使用属性上的属性应用的 `Policy` `AuthorizeAttribute` ：</span><span class="sxs-lookup"><span data-stu-id="f4105-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="f4105-125">如果要在某个要求中指定多个允许的角色，则可以将它们指定为方法的参数 `RequireRole` ：</span><span class="sxs-lookup"><span data-stu-id="f4105-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="f4105-126">此示例授权属于或角色的用户 `Administrator` `PowerUser` `BackupAdministrator` 。</span><span class="sxs-lookup"><span data-stu-id="f4105-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="f4105-127">将角色服务添加到 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="f4105-127">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="f4105-128">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="f4105-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

