---
title: ASP.NET Core 中基于声明的授权
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中添加声明授权检查。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: 6b60ae5515819b017ab577f655ed91ee4d8ed0dd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086155"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>ASP.NET Core 中基于声明的授权

<a name="security-authorization-claims-based"></a>

当创建标识可能为它分配一个或多个由受信任方颁发的声明。 声明是表示哪些使用者名称值对，没有什么方面可以执行操作。 例如，可以由本地驾驶许可证颁发机构颁发的驱动程序的许可证。 驱动程序的许可证对其具有你的出生日期。 声明名称将为这种情况下`DateOfBirth`，声明值将是您出生日期，例如`8th June 1970`和颁发者是驾驶许可证颁发机构。 基于声明的授权，简单地说，检查声明的值，并允许对此值基于资源的访问。 例如，如果你想晚上 club 访问授权过程可能为：

门安全官员则计算结果你的出生声明以及它们是否授予你访问之前信任颁发者 （驾驶许可证机构） 的日期的值。

标识可以包含多个声明包含多个值，并且可以包含多个相同类型的声明。

## <a name="adding-claims-checks"></a>添加声明检查

声明基于的授权检查声明性-开发人员将其嵌入在其代码中，对控制器或在控制器内的动作指定当前用户必须拥有，并且可以选择声明的值必须保留访问声明请求的资源。 要求是基于策略的声明，开发人员必须生成和注册表达的声明要求的策略。

最简单的类型声明的声明存在的策略看起来并不会检查值。

首先需要生成和注册策略。 这是作为授权服务配置的一部分，它通常使用一部分`ConfigureServices()`在您*Startup.cs*文件。

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

在这种情况下`EmployeeOnly`策略将检查是否存在`EmployeeNumber`上当前的标识声明。

然后应用策略使用`Policy`属性上的`AuthorizeAttribute`属性来指定策略名称;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute`特性可应用于整个控制器，在这种情况，仅匹配策略的标识将在控制器上允许任何操作的访问权限。

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

如果必须由受保护的控制器`AuthorizeAttribute`属性，但想要允许匿名访问你应用的特定操作`AllowAnonymousAttribute`属性。

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

大多数声明都具有一个值。 在创建策略时，可以指定允许的值的列表。 下面的示例将成功执行为员工的员工编号是 1、 2、 3、 4 或 5。

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

### <a name="add-a-generic-claim-check"></a>添加泛型声明检查

如果该声明值不是单个值或转换是必需的使用[RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)。 有关详细信息，请参阅[func 用于满足策略](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy)。

## <a name="multiple-policy-evaluation"></a>多个策略评估

如果将多个策略应用到控制器或操作，然后授予访问权限之前也必须通过所有策略。 例如：

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

在上述示例中任何标识了满足`EmployeeOnly`策略可以访问`Payslip`控制器中强制实施该策略的操作。 但是若要调用`UpdateSalary`操作标识必须满足*同时*`EmployeeOnly`策略和`HumanResources`策略。

如果您希望更复杂的策略，如将出生声明的日期，计算从该年龄，然后检查年龄为 21 或更低版本，则您需要自己编写[自定义策略处理程序](xref:security/authorization/policies)。
