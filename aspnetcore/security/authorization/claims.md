---
title: ASP.NET Core 中的基于声明的授权
author: rick-anderson
description: 了解如何在 ASP.NET Core 的应用程序中添加对授权的声明检查。
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
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060269"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="0cf08-103">ASP.NET Core 中的基于声明的授权</span><span class="sxs-lookup"><span data-stu-id="0cf08-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="0cf08-104">创建标识后，可以为其分配一个或多个由受信任方颁发的声明。</span><span class="sxs-lookup"><span data-stu-id="0cf08-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="0cf08-105">声明是表示使用者的名称值对，而不是主题可执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0cf08-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="0cf08-106">例如，你可能有一个由本地驾驶许可证机构颁发的驾照。</span><span class="sxs-lookup"><span data-stu-id="0cf08-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="0cf08-107">你的驱动程序许可证有你的出生日期。</span><span class="sxs-lookup"><span data-stu-id="0cf08-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="0cf08-108">在这种情况下，声明名称为 `DateOfBirth` ，声明值将是你的出生日期，例如， `8th June 1970` 颁发者为驾驶许可证颁发机构。</span><span class="sxs-lookup"><span data-stu-id="0cf08-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="0cf08-109">基于声明的授权最简单的检查声明的值并允许基于该值的资源访问。</span><span class="sxs-lookup"><span data-stu-id="0cf08-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="0cf08-110">例如，如果你想要访问晚间俱乐部，授权过程可能是：</span><span class="sxs-lookup"><span data-stu-id="0cf08-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="0cf08-111">门安全官员会评估你的出生日期的价值，以及他们是否信任颁发者 (驾照颁发机构) ，然后再授予你访问权限。</span><span class="sxs-lookup"><span data-stu-id="0cf08-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="0cf08-112">标识可包含多个值的多个声明，并且可以包含同一类型的多个声明。</span><span class="sxs-lookup"><span data-stu-id="0cf08-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="0cf08-113">添加声明检查</span><span class="sxs-lookup"><span data-stu-id="0cf08-113">Adding claims checks</span></span>

<span data-ttu-id="0cf08-114">基于声明的授权检查是声明性的：开发人员将其嵌入到代码中，针对控制器或控制器中的操作，指定当前用户必须拥有的声明，并选择性地指定声明必须持有的值才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="0cf08-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="0cf08-115">声明要求基于策略，开发人员必须构建并注册一个表示声明要求的策略。</span><span class="sxs-lookup"><span data-stu-id="0cf08-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="0cf08-116">最简单类型的声明策略将查找声明是否存在，而不检查值。</span><span class="sxs-lookup"><span data-stu-id="0cf08-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="0cf08-117">首先需要构建并注册策略。</span><span class="sxs-lookup"><span data-stu-id="0cf08-117">First you need to build and register the policy.</span></span> <span data-ttu-id="0cf08-118">这会作为授权服务配置的一部分进行，此配置通常会在 `ConfigureServices()` *Startup.cs* 文件中加入。</span><span class="sxs-lookup"><span data-stu-id="0cf08-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

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

<span data-ttu-id="0cf08-119">在这种情况下， `EmployeeOnly` 策略会检查 `EmployeeNumber` 当前标识上是否存在声明。</span><span class="sxs-lookup"><span data-stu-id="0cf08-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="0cf08-120">然后，使用属性 `Policy` 上的属性 `AuthorizeAttribute` 来指定策略名称;</span><span class="sxs-lookup"><span data-stu-id="0cf08-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="0cf08-121">该 `AuthorizeAttribute` 属性可应用于整个控制器，在此实例中，只允许与策略匹配的标识访问控制器上的任何操作。</span><span class="sxs-lookup"><span data-stu-id="0cf08-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="0cf08-122">如果有受属性保护的控制器 `AuthorizeAttribute` ，但希望允许匿名访问特定操作，请应用该 `AllowAnonymousAttribute` 属性。</span><span class="sxs-lookup"><span data-stu-id="0cf08-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="0cf08-123">大多数声明都包含一个值。</span><span class="sxs-lookup"><span data-stu-id="0cf08-123">Most claims come with a value.</span></span> <span data-ttu-id="0cf08-124">可以在创建策略时指定允许值的列表。</span><span class="sxs-lookup"><span data-stu-id="0cf08-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="0cf08-125">以下示例仅对雇员编号为1、2、3、4或5的雇员成功。</span><span class="sxs-lookup"><span data-stu-id="0cf08-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

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
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="0cf08-126">添加泛型声明检查</span><span class="sxs-lookup"><span data-stu-id="0cf08-126">Add a generic claim check</span></span>

<span data-ttu-id="0cf08-127">如果声明值不是单个值或需要转换，请使用 [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)。</span><span class="sxs-lookup"><span data-stu-id="0cf08-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="0cf08-128">有关详细信息，请参阅 [使用 func 来实现策略](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy)。</span><span class="sxs-lookup"><span data-stu-id="0cf08-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="0cf08-129">多个策略评估</span><span class="sxs-lookup"><span data-stu-id="0cf08-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="0cf08-130">如果将多个策略应用于控制器或操作，则在授予访问权限之前，所有策略都必须通过。</span><span class="sxs-lookup"><span data-stu-id="0cf08-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="0cf08-131">例如： 。</span><span class="sxs-lookup"><span data-stu-id="0cf08-131">For example:</span></span>

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

<span data-ttu-id="0cf08-132">在上面的示例中，满足策略的任何标识 `EmployeeOnly` 都可以访问该 `Payslip` 操作，因为该策略是在控制器上强制实施的。</span><span class="sxs-lookup"><span data-stu-id="0cf08-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="0cf08-133">但是，若要调用 `UpdateSalary` 操作，标识必须 *同时* 满足 `EmployeeOnly` 策略和 `HumanResources` 策略。</span><span class="sxs-lookup"><span data-stu-id="0cf08-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="0cf08-134">如果需要更复杂的策略，例如拍摄一个出生日期、计算该日期的年龄，然后检查年龄是否为21或更低，则需要编写 [自定义策略处理程序](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="0cf08-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
