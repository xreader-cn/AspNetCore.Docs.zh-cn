---
title: ASP.NET核心中的基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序，以在 ASP.NET 酷应用中强制实施授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488756"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="69c84-103">ASP.NET核心中的基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="69c84-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="69c84-104">在封面下方，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)使用要求、需求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="69c84-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="69c84-105">这些构建基块支持代码中授权评估的表达式。</span><span class="sxs-lookup"><span data-stu-id="69c84-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="69c84-106">结果是更丰富、可重用、可测试的授权结构。</span><span class="sxs-lookup"><span data-stu-id="69c84-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="69c84-107">授权策略由一个或多个要求组成。</span><span class="sxs-lookup"><span data-stu-id="69c84-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="69c84-108">它在 方法中注册为授权服务配置的一`Startup.ConfigureServices`部分：</span><span class="sxs-lookup"><span data-stu-id="69c84-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="69c84-109">在前面的示例中，将创建一个"AtLeast21"策略。</span><span class="sxs-lookup"><span data-stu-id="69c84-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="69c84-110">它有一个最低年龄&mdash;的单一要求，作为要求的参数提供。</span><span class="sxs-lookup"><span data-stu-id="69c84-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="69c84-111">I 授权服务</span><span class="sxs-lookup"><span data-stu-id="69c84-111">IAuthorizationService</span></span> 

<span data-ttu-id="69c84-112">确定授权是否成功的主要服务是<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>：</span><span class="sxs-lookup"><span data-stu-id="69c84-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="69c84-113">前面的代码突出显示了[I 授权服务的](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)两种方法。</span><span class="sxs-lookup"><span data-stu-id="69c84-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="69c84-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是一个没有方法的标记服务，是跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="69c84-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="69c84-115">每个<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>公司负责检查是否满足要求：</span><span class="sxs-lookup"><span data-stu-id="69c84-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="69c84-116">类<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>是处理程序用于标记是否满足要求的内容：</span><span class="sxs-lookup"><span data-stu-id="69c84-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="69c84-117">以下代码显示授权服务的简化（并带有注释）默认实现：</span><span class="sxs-lookup"><span data-stu-id="69c84-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="69c84-118">以下代码显示了典型的`ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="69c84-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="69c84-119">使用<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>或`[Authorize(Policy = "Something")]`授权。</span><span class="sxs-lookup"><span data-stu-id="69c84-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="69c84-120">将策略应用于 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="69c84-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="69c84-121">如果您使用的是 Razor 页面，请参阅本文档中[将策略应用于 Razor 页面](#applying-policies-to-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="69c84-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="69c84-122">策略通过使用具有策略名称`[Authorize]`的属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="69c84-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="69c84-123">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="69c84-124">将策略应用于剃刀页面</span><span class="sxs-lookup"><span data-stu-id="69c84-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="69c84-125">策略通过使用具有策略名称`[Authorize]`的属性应用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="69c84-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="69c84-126">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="69c84-127">策略也可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization)应用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="69c84-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="69c84-128">要求</span><span class="sxs-lookup"><span data-stu-id="69c84-128">Requirements</span></span>

<span data-ttu-id="69c84-129">授权要求是策略可用于评估当前用户主体的数据参数的集合。</span><span class="sxs-lookup"><span data-stu-id="69c84-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="69c84-130">在我们的"AtLeast21"政策中，要求是最小年龄的单个&mdash;参数。</span><span class="sxs-lookup"><span data-stu-id="69c84-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="69c84-131">要求实现[I 授权要求](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。</span><span class="sxs-lookup"><span data-stu-id="69c84-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="69c84-132">参数化的最低年龄要求可执行如下：</span><span class="sxs-lookup"><span data-stu-id="69c84-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="69c84-133">如果授权策略包含多个授权要求，则必须通过所有要求才能成功策略评估。</span><span class="sxs-lookup"><span data-stu-id="69c84-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="69c84-134">换句话说，添加到单个授权策略中的多个授权要求将基于**AND**处理。</span><span class="sxs-lookup"><span data-stu-id="69c84-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="69c84-135">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="69c84-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="69c84-136">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="69c84-136">Authorization handlers</span></span>

<span data-ttu-id="69c84-137">授权处理程序负责评估需求的属性。</span><span class="sxs-lookup"><span data-stu-id="69c84-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="69c84-138">授权处理程序根据提供的[授权处理程序计算](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)需求，以确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="69c84-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="69c84-139">要求可以[有多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="69c84-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="69c84-140">处理程序可以继承[授权处理程序\<t要求>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中`TRequirement`需要处理。</span><span class="sxs-lookup"><span data-stu-id="69c84-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="69c84-141">或者，处理程序可以实现[I授权处理程序](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。</span><span class="sxs-lookup"><span data-stu-id="69c84-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="69c84-142">对一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="69c84-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="69c84-143">下面是一对一关系的示例，其中最小年龄处理程序使用单个要求：</span><span class="sxs-lookup"><span data-stu-id="69c84-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="69c84-144">前面的代码确定当前用户主体是否具有由已知和受信任的颁发者颁发的出生日期声明。</span><span class="sxs-lookup"><span data-stu-id="69c84-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="69c84-145">当缺少声明时，无法发生授权，在这种情况下，将返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="69c84-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="69c84-146">当声明存在时，将计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="69c84-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="69c84-147">如果用户满足要求定义的最低年龄，则授权被视为成功。</span><span class="sxs-lookup"><span data-stu-id="69c84-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="69c84-148">当授权成功时，`context.Succeed`将调用满足的要求作为其唯一参数。</span><span class="sxs-lookup"><span data-stu-id="69c84-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="69c84-149">对多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="69c84-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="69c84-150">下面是一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：</span><span class="sxs-lookup"><span data-stu-id="69c84-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="69c84-151">前面的代码遍历[Pending要求](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;包含未标记为成功的要求的属性。</span><span class="sxs-lookup"><span data-stu-id="69c84-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="69c84-152">对于要求`ReadPermission`，用户必须是所有者或发起人才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="69c84-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="69c84-153">如果是`EditPermission`或`DeletePermission`要求，他或她必须是访问请求的资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="69c84-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="69c84-154">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="69c84-154">Handler registration</span></span>

<span data-ttu-id="69c84-155">在配置期间，处理程序在服务集合中注册。</span><span class="sxs-lookup"><span data-stu-id="69c84-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="69c84-156">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="69c84-157">前面的代码通过调用`MinimumAgeHandler``services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`注册为单例。</span><span class="sxs-lookup"><span data-stu-id="69c84-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="69c84-158">处理程序可以使用任何内置[服务生存期进行](xref:fundamentals/dependency-injection#service-lifetimes)注册。</span><span class="sxs-lookup"><span data-stu-id="69c84-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="69c84-159">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="69c84-159">What should a handler return?</span></span>

<span data-ttu-id="69c84-160">请注意，`Handle`[处理程序示例中](#security-authorization-handler-example)的方法不返回任何值。</span><span class="sxs-lookup"><span data-stu-id="69c84-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="69c84-161">如何指示成功或失败的状态？</span><span class="sxs-lookup"><span data-stu-id="69c84-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="69c84-162">处理程序通过调用`context.Succeed(IAuthorizationRequirement requirement)`来表示成功，传递已成功验证的要求。</span><span class="sxs-lookup"><span data-stu-id="69c84-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="69c84-163">处理程序通常不需要处理故障，因为相同要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="69c84-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="69c84-164">为了保证失败，即使其他需求处理程序成功，请调用`context.Fail`。</span><span class="sxs-lookup"><span data-stu-id="69c84-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="69c84-165">如果处理程序调用`context.Succeed`或`context.Fail`，仍调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="69c84-166">这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或未通过要求也是如此。</span><span class="sxs-lookup"><span data-stu-id="69c84-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="69c84-167">当设置为`false`[时，InvokeHandlersAfter 失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（在 ASP.NET Core 1.1 和更高版本中可用）在调用时`context.Fail`短路处理程序的执行。</span><span class="sxs-lookup"><span data-stu-id="69c84-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="69c84-168">`InvokeHandlersAfterFailure`默认值为`true`，在这种情况下，将调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="69c84-169">即使身份验证失败，也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="69c84-170">为什么需要多个处理程序来满足需求？</span><span class="sxs-lookup"><span data-stu-id="69c84-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="69c84-171">如果希望评估基于**OR，** 则为单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="69c84-172">例如，微软的大门只打开钥匙卡。</span><span class="sxs-lookup"><span data-stu-id="69c84-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="69c84-173">如果您将钥匙卡留在家中，接待员会打印临时贴纸，并为您开门。</span><span class="sxs-lookup"><span data-stu-id="69c84-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="69c84-174">在这种情况下，您将有一个要求，*即"构建入口*"，但有多个处理程序，每个处理程序都检查单个要求。</span><span class="sxs-lookup"><span data-stu-id="69c84-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="69c84-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="69c84-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="69c84-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="69c84-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="69c84-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="69c84-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="69c84-178">确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="69c84-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="69c84-179">如果任一处理程序在策略计算 时成功`BuildingEntryRequirement`，则策略计算将成功。</span><span class="sxs-lookup"><span data-stu-id="69c84-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="69c84-180">使用 func 实现策略</span><span class="sxs-lookup"><span data-stu-id="69c84-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="69c84-181">在某些情况下，实现策略在代码中很容易表达。</span><span class="sxs-lookup"><span data-stu-id="69c84-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="69c84-182">在与策略生成器配置策略时，`Func<AuthorizationHandlerContext, bool>``RequireAssertion`可以提供 .</span><span class="sxs-lookup"><span data-stu-id="69c84-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="69c84-183">例如，可以重写前`BadgeEntryHandler`一个，如下所示：</span><span class="sxs-lookup"><span data-stu-id="69c84-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="69c84-184">在处理程序中访问 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="69c84-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="69c84-185">在`HandleRequirementAsync`授权处理程序中实现的方法有两个参数：和`AuthorizationHandlerContext``TRequirement`正在处理的参数。</span><span class="sxs-lookup"><span data-stu-id="69c84-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="69c84-186">MVC 或 Jabbr 等框架可免费向`Resource`的属性添加任何对象`AuthorizationHandlerContext`以传递额外信息。</span><span class="sxs-lookup"><span data-stu-id="69c84-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="69c84-187">例如，MVC 在属性中传递[授权筛选器上下文](/dotnet/api/?term=AuthorizationFilterContext)的`Resource`实例。</span><span class="sxs-lookup"><span data-stu-id="69c84-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="69c84-188">此属性提供对`HttpContext`的`RouteData`访问权限，以及 MVC 和 Razor 页面提供的所有内容。</span><span class="sxs-lookup"><span data-stu-id="69c84-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="69c84-189">`Resource`属性的使用是特定于框架的。</span><span class="sxs-lookup"><span data-stu-id="69c84-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="69c84-190">使用 属性中`Resource`的信息会将授权策略限制为特定框架。</span><span class="sxs-lookup"><span data-stu-id="69c84-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="69c84-191">应使用`Resource``is`关键字强制转换属性，然后确认强制转换已成功确保代码在其他框架上运行时不会崩溃与 。 `InvalidCastException`</span><span class="sxs-lookup"><span data-stu-id="69c84-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="69c84-192">在封面下方，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)使用要求、需求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="69c84-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="69c84-193">这些构建基块支持代码中授权评估的表达式。</span><span class="sxs-lookup"><span data-stu-id="69c84-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="69c84-194">结果是更丰富、可重用、可测试的授权结构。</span><span class="sxs-lookup"><span data-stu-id="69c84-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="69c84-195">授权策略由一个或多个要求组成。</span><span class="sxs-lookup"><span data-stu-id="69c84-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="69c84-196">它在 方法中注册为授权服务配置的一`Startup.ConfigureServices`部分：</span><span class="sxs-lookup"><span data-stu-id="69c84-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="69c84-197">在前面的示例中，将创建一个"AtLeast21"策略。</span><span class="sxs-lookup"><span data-stu-id="69c84-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="69c84-198">它有一个最低年龄&mdash;的单一要求，作为要求的参数提供。</span><span class="sxs-lookup"><span data-stu-id="69c84-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="69c84-199">I 授权服务</span><span class="sxs-lookup"><span data-stu-id="69c84-199">IAuthorizationService</span></span> 

<span data-ttu-id="69c84-200">确定授权是否成功的主要服务是<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>：</span><span class="sxs-lookup"><span data-stu-id="69c84-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="69c84-201">前面的代码突出显示了[I 授权服务的](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)两种方法。</span><span class="sxs-lookup"><span data-stu-id="69c84-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="69c84-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是一个没有方法的标记服务，是跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="69c84-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="69c84-203">每个<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>公司负责检查是否满足要求：</span><span class="sxs-lookup"><span data-stu-id="69c84-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="69c84-204">类<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>是处理程序用于标记是否满足要求的内容：</span><span class="sxs-lookup"><span data-stu-id="69c84-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="69c84-205">以下代码显示授权服务的简化（并带有注释）默认实现：</span><span class="sxs-lookup"><span data-stu-id="69c84-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="69c84-206">以下代码显示了典型的`ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="69c84-206">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="69c84-207">使用<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>或`[Authorize(Policy = "Something")]`授权。</span><span class="sxs-lookup"><span data-stu-id="69c84-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="69c84-208">将策略应用于 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="69c84-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="69c84-209">如果您使用的是 Razor 页面，请参阅本文档中[将策略应用于 Razor 页面](#applying-policies-to-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="69c84-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="69c84-210">策略通过使用具有策略名称`[Authorize]`的属性应用于控制器。</span><span class="sxs-lookup"><span data-stu-id="69c84-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="69c84-211">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="69c84-212">将策略应用于剃刀页面</span><span class="sxs-lookup"><span data-stu-id="69c84-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="69c84-213">策略通过使用具有策略名称`[Authorize]`的属性应用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="69c84-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="69c84-214">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="69c84-215">策略也可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization)应用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="69c84-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="69c84-216">要求</span><span class="sxs-lookup"><span data-stu-id="69c84-216">Requirements</span></span>

<span data-ttu-id="69c84-217">授权要求是策略可用于评估当前用户主体的数据参数的集合。</span><span class="sxs-lookup"><span data-stu-id="69c84-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="69c84-218">在我们的"AtLeast21"政策中，要求是最小年龄的单个&mdash;参数。</span><span class="sxs-lookup"><span data-stu-id="69c84-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="69c84-219">要求实现[I 授权要求](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。</span><span class="sxs-lookup"><span data-stu-id="69c84-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="69c84-220">参数化的最低年龄要求可执行如下：</span><span class="sxs-lookup"><span data-stu-id="69c84-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="69c84-221">如果授权策略包含多个授权要求，则必须通过所有要求才能成功策略评估。</span><span class="sxs-lookup"><span data-stu-id="69c84-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="69c84-222">换句话说，添加到单个授权策略中的多个授权要求将基于**AND**处理。</span><span class="sxs-lookup"><span data-stu-id="69c84-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="69c84-223">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="69c84-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="69c84-224">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="69c84-224">Authorization handlers</span></span>

<span data-ttu-id="69c84-225">授权处理程序负责评估需求的属性。</span><span class="sxs-lookup"><span data-stu-id="69c84-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="69c84-226">授权处理程序根据提供的[授权处理程序计算](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)需求，以确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="69c84-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="69c84-227">要求可以[有多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="69c84-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="69c84-228">处理程序可以继承[授权处理程序\<t要求>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中`TRequirement`需要处理。</span><span class="sxs-lookup"><span data-stu-id="69c84-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="69c84-229">或者，处理程序可以实现[I授权处理程序](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。</span><span class="sxs-lookup"><span data-stu-id="69c84-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="69c84-230">对一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="69c84-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="69c84-231">下面是一对一关系的示例，其中最小年龄处理程序使用单个要求：</span><span class="sxs-lookup"><span data-stu-id="69c84-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="69c84-232">前面的代码确定当前用户主体是否具有由已知和受信任的颁发者颁发的出生日期声明。</span><span class="sxs-lookup"><span data-stu-id="69c84-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="69c84-233">当缺少声明时，无法发生授权，在这种情况下，将返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="69c84-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="69c84-234">当声明存在时，将计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="69c84-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="69c84-235">如果用户满足要求定义的最低年龄，则授权被视为成功。</span><span class="sxs-lookup"><span data-stu-id="69c84-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="69c84-236">当授权成功时，`context.Succeed`将调用满足的要求作为其唯一参数。</span><span class="sxs-lookup"><span data-stu-id="69c84-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="69c84-237">对多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="69c84-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="69c84-238">下面是一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：</span><span class="sxs-lookup"><span data-stu-id="69c84-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="69c84-239">前面的代码遍历[Pending要求](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;包含未标记为成功的要求的属性。</span><span class="sxs-lookup"><span data-stu-id="69c84-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="69c84-240">对于要求`ReadPermission`，用户必须是所有者或发起人才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="69c84-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="69c84-241">如果是`EditPermission`或`DeletePermission`要求，他或她必须是访问请求的资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="69c84-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="69c84-242">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="69c84-242">Handler registration</span></span>

<span data-ttu-id="69c84-243">在配置期间，处理程序在服务集合中注册。</span><span class="sxs-lookup"><span data-stu-id="69c84-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="69c84-244">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="69c84-245">前面的代码通过调用`MinimumAgeHandler``services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`注册为单例。</span><span class="sxs-lookup"><span data-stu-id="69c84-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="69c84-246">处理程序可以使用任何内置[服务生存期进行](xref:fundamentals/dependency-injection#service-lifetimes)注册。</span><span class="sxs-lookup"><span data-stu-id="69c84-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="69c84-247">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="69c84-247">What should a handler return?</span></span>

<span data-ttu-id="69c84-248">请注意，`Handle`[处理程序示例中](#security-authorization-handler-example)的方法不返回任何值。</span><span class="sxs-lookup"><span data-stu-id="69c84-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="69c84-249">如何指示成功或失败的状态？</span><span class="sxs-lookup"><span data-stu-id="69c84-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="69c84-250">处理程序通过调用`context.Succeed(IAuthorizationRequirement requirement)`来表示成功，传递已成功验证的要求。</span><span class="sxs-lookup"><span data-stu-id="69c84-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="69c84-251">处理程序通常不需要处理故障，因为相同要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="69c84-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="69c84-252">为了保证失败，即使其他需求处理程序成功，请调用`context.Fail`。</span><span class="sxs-lookup"><span data-stu-id="69c84-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="69c84-253">如果处理程序调用`context.Succeed`或`context.Fail`，仍调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="69c84-254">这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或未通过要求也是如此。</span><span class="sxs-lookup"><span data-stu-id="69c84-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="69c84-255">当设置为`false`[时，InvokeHandlersAfter 失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（在 ASP.NET Core 1.1 和更高版本中可用）在调用时`context.Fail`短路处理程序的执行。</span><span class="sxs-lookup"><span data-stu-id="69c84-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="69c84-256">`InvokeHandlersAfterFailure`默认值为`true`，在这种情况下，将调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="69c84-257">即使身份验证失败，也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="69c84-258">为什么需要多个处理程序来满足需求？</span><span class="sxs-lookup"><span data-stu-id="69c84-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="69c84-259">如果希望评估基于**OR，** 则为单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="69c84-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="69c84-260">例如，微软的大门只打开钥匙卡。</span><span class="sxs-lookup"><span data-stu-id="69c84-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="69c84-261">如果您将钥匙卡留在家中，接待员会打印临时贴纸，并为您开门。</span><span class="sxs-lookup"><span data-stu-id="69c84-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="69c84-262">在这种情况下，您将有一个要求，*即"构建入口*"，但有多个处理程序，每个处理程序都检查单个要求。</span><span class="sxs-lookup"><span data-stu-id="69c84-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="69c84-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="69c84-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="69c84-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="69c84-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="69c84-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="69c84-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="69c84-266">确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="69c84-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="69c84-267">如果任一处理程序在策略计算 时成功`BuildingEntryRequirement`，则策略计算将成功。</span><span class="sxs-lookup"><span data-stu-id="69c84-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="69c84-268">使用 func 实现策略</span><span class="sxs-lookup"><span data-stu-id="69c84-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="69c84-269">在某些情况下，实现策略在代码中很容易表达。</span><span class="sxs-lookup"><span data-stu-id="69c84-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="69c84-270">在与策略生成器配置策略时，`Func<AuthorizationHandlerContext, bool>``RequireAssertion`可以提供 .</span><span class="sxs-lookup"><span data-stu-id="69c84-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="69c84-271">例如，可以重写前`BadgeEntryHandler`一个，如下所示：</span><span class="sxs-lookup"><span data-stu-id="69c84-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="69c84-272">在处理程序中访问 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="69c84-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="69c84-273">在`HandleRequirementAsync`授权处理程序中实现的方法有两个参数：和`AuthorizationHandlerContext``TRequirement`正在处理的参数。</span><span class="sxs-lookup"><span data-stu-id="69c84-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="69c84-274">MVC 或 SignalR 等框架可免费向`Resource`的属性添加任何对象`AuthorizationHandlerContext`以传递额外信息。</span><span class="sxs-lookup"><span data-stu-id="69c84-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="69c84-275">使用终结点路由时，授权通常由授权中间件处理。</span><span class="sxs-lookup"><span data-stu-id="69c84-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="69c84-276">在这种情况下，`Resource`属性是 的<xref:Microsoft.AspNetCore.Http.Endpoint>实例。</span><span class="sxs-lookup"><span data-stu-id="69c84-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="69c84-277">终结点可用于探测要路由到的资源的基础。</span><span class="sxs-lookup"><span data-stu-id="69c84-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="69c84-278">例如：</span><span class="sxs-lookup"><span data-stu-id="69c84-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="69c84-279">对于传统路由，或者当授权作为 MVC 授权筛选器的一部分发生时，值`Resource`是实例。 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext></span><span class="sxs-lookup"><span data-stu-id="69c84-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="69c84-280">此属性提供对`HttpContext`的`RouteData`访问权限，以及 MVC 和 Razor 页面提供的所有内容。</span><span class="sxs-lookup"><span data-stu-id="69c84-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="69c84-281">`Resource`属性的使用是特定于框架的。</span><span class="sxs-lookup"><span data-stu-id="69c84-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="69c84-282">使用 属性中`Resource`的信息会将授权策略限制为特定框架。</span><span class="sxs-lookup"><span data-stu-id="69c84-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="69c84-283">应使用`Resource``is`关键字强制转换属性，然后确认强制转换已成功确保代码在其他框架上运行时不会崩溃与 。 `InvalidCastException`</span><span class="sxs-lookup"><span data-stu-id="69c84-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
