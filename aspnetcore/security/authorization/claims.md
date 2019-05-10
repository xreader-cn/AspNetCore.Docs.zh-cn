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
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="db5b8-103">ASP.NET Core 中基于声明的授权</span><span class="sxs-lookup"><span data-stu-id="db5b8-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="db5b8-104">当创建标识可能为它分配一个或多个由受信任方颁发的声明。</span><span class="sxs-lookup"><span data-stu-id="db5b8-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="db5b8-105">声明是表示哪些使用者名称值对，没有什么方面可以执行操作。</span><span class="sxs-lookup"><span data-stu-id="db5b8-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="db5b8-106">例如，可以由本地驾驶许可证颁发机构颁发的驱动程序的许可证。</span><span class="sxs-lookup"><span data-stu-id="db5b8-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="db5b8-107">驱动程序的许可证对其具有你的出生日期。</span><span class="sxs-lookup"><span data-stu-id="db5b8-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="db5b8-108">声明名称将为这种情况下`DateOfBirth`，声明值将是您出生日期，例如`8th June 1970`和颁发者是驾驶许可证颁发机构。</span><span class="sxs-lookup"><span data-stu-id="db5b8-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="db5b8-109">基于声明的授权，简单地说，检查声明的值，并允许对此值基于资源的访问。</span><span class="sxs-lookup"><span data-stu-id="db5b8-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="db5b8-110">例如，如果你想晚上 club 访问授权过程可能为：</span><span class="sxs-lookup"><span data-stu-id="db5b8-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="db5b8-111">门安全官员则计算结果你的出生声明以及它们是否授予你访问之前信任颁发者 （驾驶许可证机构） 的日期的值。</span><span class="sxs-lookup"><span data-stu-id="db5b8-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="db5b8-112">标识可以包含多个声明包含多个值，并且可以包含多个相同类型的声明。</span><span class="sxs-lookup"><span data-stu-id="db5b8-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="db5b8-113">添加声明检查</span><span class="sxs-lookup"><span data-stu-id="db5b8-113">Adding claims checks</span></span>

<span data-ttu-id="db5b8-114">声明基于的授权检查声明性-开发人员将其嵌入在其代码中，对控制器或在控制器内的动作指定当前用户必须拥有，并且可以选择声明的值必须保留访问声明请求的资源。</span><span class="sxs-lookup"><span data-stu-id="db5b8-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="db5b8-115">要求是基于策略的声明，开发人员必须生成和注册表达的声明要求的策略。</span><span class="sxs-lookup"><span data-stu-id="db5b8-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="db5b8-116">最简单的类型声明的声明存在的策略看起来并不会检查值。</span><span class="sxs-lookup"><span data-stu-id="db5b8-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="db5b8-117">首先需要生成和注册策略。</span><span class="sxs-lookup"><span data-stu-id="db5b8-117">First you need to build and register the policy.</span></span> <span data-ttu-id="db5b8-118">这是作为授权服务配置的一部分，它通常使用一部分`ConfigureServices()`在您*Startup.cs*文件。</span><span class="sxs-lookup"><span data-stu-id="db5b8-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="db5b8-119">在这种情况下`EmployeeOnly`策略将检查是否存在`EmployeeNumber`上当前的标识声明。</span><span class="sxs-lookup"><span data-stu-id="db5b8-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="db5b8-120">然后应用策略使用`Policy`属性上的`AuthorizeAttribute`属性来指定策略名称;</span><span class="sxs-lookup"><span data-stu-id="db5b8-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="db5b8-121">`AuthorizeAttribute`特性可应用于整个控制器，在这种情况，仅匹配策略的标识将在控制器上允许任何操作的访问权限。</span><span class="sxs-lookup"><span data-stu-id="db5b8-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="db5b8-122">如果必须由受保护的控制器`AuthorizeAttribute`属性，但想要允许匿名访问你应用的特定操作`AllowAnonymousAttribute`属性。</span><span class="sxs-lookup"><span data-stu-id="db5b8-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="db5b8-123">大多数声明都具有一个值。</span><span class="sxs-lookup"><span data-stu-id="db5b8-123">Most claims come with a value.</span></span> <span data-ttu-id="db5b8-124">在创建策略时，可以指定允许的值的列表。</span><span class="sxs-lookup"><span data-stu-id="db5b8-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="db5b8-125">下面的示例将成功执行为员工的员工编号是 1、 2、 3、 4 或 5。</span><span class="sxs-lookup"><span data-stu-id="db5b8-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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

### <a name="add-a-generic-claim-check"></a><span data-ttu-id="db5b8-126">添加泛型声明检查</span><span class="sxs-lookup"><span data-stu-id="db5b8-126">Add a generic claim check</span></span>

<span data-ttu-id="db5b8-127">如果该声明值不是单个值或转换是必需的使用[RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)。</span><span class="sxs-lookup"><span data-stu-id="db5b8-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="db5b8-128">有关详细信息，请参阅[func 用于满足策略](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy)。</span><span class="sxs-lookup"><span data-stu-id="db5b8-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="db5b8-129">多个策略评估</span><span class="sxs-lookup"><span data-stu-id="db5b8-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="db5b8-130">如果将多个策略应用到控制器或操作，然后授予访问权限之前也必须通过所有策略。</span><span class="sxs-lookup"><span data-stu-id="db5b8-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="db5b8-131">例如：</span><span class="sxs-lookup"><span data-stu-id="db5b8-131">For example:</span></span>

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

<span data-ttu-id="db5b8-132">在上述示例中任何标识了满足`EmployeeOnly`策略可以访问`Payslip`控制器中强制实施该策略的操作。</span><span class="sxs-lookup"><span data-stu-id="db5b8-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="db5b8-133">但是若要调用`UpdateSalary`操作标识必须满足*同时*`EmployeeOnly`策略和`HumanResources`策略。</span><span class="sxs-lookup"><span data-stu-id="db5b8-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="db5b8-134">如果您希望更复杂的策略，如将出生声明的日期，计算从该年龄，然后检查年龄为 21 或更低版本，则您需要自己编写[自定义策略处理程序](xref:security/authorization/policies)。</span><span class="sxs-lookup"><span data-stu-id="db5b8-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
