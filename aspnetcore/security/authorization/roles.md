---
title: ASP.NET Core 中基于角色的授权
author: rick-anderson
description: 了解如何通过将角色传递给 Authorize 属性限制 ASP.NET Core 控制器和操作访问。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651636"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core 中基于角色的授权

<a name="security-authorization-role-based"></a>

创建标识时，它可能属于一个或多个角色。 例如，Tracy 可能属于管理员角色和用户角色，但 Scott 可能只属于用户角色。 如何创建和管理这些角色取决于授权过程的后备存储。 角色通过[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)类的[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)方法向开发人员公开。

## <a name="adding-role-checks"></a>添加角色检查

基于角色的授权检查是声明性&mdash;开发人员将其嵌入到代码中，针对控制器或控制器中的操作，指定当前用户必须是其成员的角色才能访问请求的资源。

例如，以下代码将对 `AdministrationController` 上的任何操作的访问权限限制为作为 `Administrator` 角色成员的用户：

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

可以将多个角色指定为逗号分隔列表：

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

只有作为 `HRManager` 角色成员的用户或 `Finance` 角色的成员才能访问此控制器。

如果应用多个属性，则访问用户必须是所有指定角色的成员;下面的示例要求用户必须同时是 `PowerUser` 和 `ControlPanelUser` 角色的成员。

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

您可以通过在操作级别应用其他角色授权属性来进一步限制访问权限：

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

在前面的代码片段中，`Administrator` 角色或 `PowerUser` 角色的成员可以访问控制器和 `SetTime` 操作，但只有 `Administrator` 角色的成员才能访问 `ShutDown` 操作。

你还可以锁定控制器，但允许对单个操作进行匿名、未经身份验证的访问。

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

对于 Razor Pages，可以通过以下任一方法应用 `AuthorizeAttribute`：

* 使用[约定](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)，或
* 将 `AuthorizeAttribute` 应用到 `PageModel` 实例：

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
> 筛选器属性（包括 `AuthorizeAttribute`）只能应用于 PageModel，而不能应用于特定页面处理程序方法。
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>基于策略的角色检查

还可以使用新策略语法来表示角色要求，开发人员可在其中将在启动时作为授权服务配置的一部分注册策略。 这通常出现在*Startup.cs*文件 `ConfigureServices()` 中。

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

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

使用 `AuthorizeAttribute` 属性上的 `Policy` 属性应用策略：

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

如果要在某个要求中指定多个允许的角色，则可以将它们指定为 `RequireRole` 方法的参数：

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

此示例授权属于 `Administrator`、`PowerUser` 或 `BackupAdministrator` 角色的用户。

### <a name="add-role-services-to-identity"></a>将角色服务添加到标识

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)以添加角色服务：

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

