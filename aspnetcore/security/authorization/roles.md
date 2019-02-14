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
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core 中基于角色的授权

<a name="security-authorization-role-based"></a>

创建一个标识时它可能属于一个或多个角色。 例如，爱妻 Tracy 可能属于管理员和用户角色，同时 Scott 可能仅属于用户角色。 如何创建和管理这些角色取决于后备存储的授权过程。 角色公开为通过开发人员[IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole)方法[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal)类。

::: moniker range=">= aspnetcore-2.0"

> [!IMPORTANT]
> 本主题不适用于 Razor 页面。 Razor 页面支持[IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter)并[iasyncpagefilter 表示](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter)。 有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。

::: moniker-end

## <a name="adding-role-checks"></a>添加角色检查

基于角色的授权检查是声明性&mdash;开发人员将其嵌入在其代码中，对控制器或在控制器内的动作指定当前用户必须是成员的访问请求的资源的角色。

例如，下面的代码上限制任何操作的访问权限`AdministrationController`谁是其成员的用户到`Administrator`角色：

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

以逗号分隔的列表，可以指定多个角色：

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

将仅可由成员的用户访问此控制器的`HRManager`角色或`Finance`角色。

如果在应用多个属性，则访问用户必须是指定; 的所有角色的成员下面的示例要求用户必须是两个成员`PowerUser`和`ControlPanelUser`角色。

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

通过应用其他角色授权属性在操作级别，可以进一步限制访问权限：

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

中的上一代码片段成员`Administrator`角色或`PowerUser`角色可访问该控制器并`SetTime`操作，但只有的成员`Administrator`角色可以访问`ShutDown`操作。

您还可以锁定一个控制器，但允许匿名、 未经身份验证访问各项操作。

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

## <a name="policy-based-role-checks"></a>基于策略角色检查

此外可以使用新的策略语法，其中一名开发人员将策略在启动时注册为授权服务配置的一部分表示角色的要求。 这通常发生在`ConfigureServices()`在您*Startup.cs*文件。

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

使用应用策略`Policy`属性上的`AuthorizeAttribute`属性：

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

如果你想要指定多个允许的角色中一项要求，则您可以将他们指定为参数`RequireRole`方法：

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

此示例中授权用户属于`Administrator`，`PowerUser`或`BackupAdministrator`角色。

### <a name="add-role-services-to-identity"></a>将角色服务添加到标识

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]