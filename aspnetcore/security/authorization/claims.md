---
title: ASP.NET Core 中的基于声明的授权
author: rick-anderson
description: 了解如何在 ASP.NET Core 的应用程序中添加对授权的声明检查。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143433"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>ASP.NET Core 中的基于声明的授权

<a name="security-authorization-claims-based"></a>

创建标识后，可以为其分配一个或多个由受信任方颁发的声明。 声明是表示使用者的名称值对，而不是主题可执行的操作。 例如，你可能有一个由本地驾驶许可证机构颁发的驾照。 你的驱动程序许可证有你的出生日期。 在这种情况下，声明名称将 `DateOfBirth`，声明值将是你的出生日期，例如 `8th June 1970`，颁发者为驾驶许可证授权机构。 基于声明的授权最简单的检查声明的值并允许基于该值的资源访问。 例如，如果你想要访问晚间俱乐部，授权过程可能是：

门安全官员会评估你的出生日期的价值，以及他们是否信任颁发者（驾驶许可证授权），然后才授予你访问权限。

标识可包含多个值的多个声明，并且可以包含同一类型的多个声明。

## <a name="adding-claims-checks"></a>添加声明检查

基于声明的授权检查是声明性的-开发人员将其嵌入到代码中，针对控制器或控制器中的操作，指定当前用户必须拥有的声明，并选择性地指定声明必须持有的值才能访问请求的资源。 声明要求基于策略，开发人员必须构建并注册一个表示声明要求的策略。

最简单类型的声明策略将查找声明是否存在，而不检查值。

首先需要构建并注册策略。 这会在授权服务配置过程中发生，这通常会在*Startup.cs*文件中包含 `ConfigureServices()`。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

在这种情况下，`EmployeeOnly` 策略会检查当前标识上是否存在 `EmployeeNumber` 声明。

然后，使用 `AuthorizeAttribute` 属性的 `Policy` 属性来指定策略名称;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute` 特性可应用于整个控制器，在此实例中，只允许与策略匹配的标识访问控制器上的任何操作。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

如果你的控制器受 `AuthorizeAttribute` 属性保护，但希望允许匿名访问特定操作，请应用 `AllowAnonymousAttribute` 属性。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

大多数声明都包含一个值。 可以在创建策略时指定允许值的列表。 以下示例仅对雇员编号为1、2、3、4或5的雇员成功。

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>添加泛型声明检查

如果声明值不是单个值或需要转换，请使用[RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)。 有关详细信息，请参阅[使用 func 来实现策略](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy)。

## <a name="multiple-policy-evaluation"></a>多个策略评估

如果将多个策略应用于控制器或操作，则在授予访问权限之前，所有策略都必须通过。 例如:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

在上面的示例中，满足 `EmployeeOnly` 策略的任何标识都可以访问 `Payslip` 操作，因为该策略是在控制器上强制实施的。 但是，若要调用 `UpdateSalary` 操作，标识必须*同时*满足 `EmployeeOnly` 策略和 `HumanResources` 策略。

如果需要更复杂的策略，例如拍摄一个出生日期、计算该日期的年龄，然后检查年龄是否为21或更低，则需要编写[自定义策略处理程序](xref:security/authorization/policies)。
