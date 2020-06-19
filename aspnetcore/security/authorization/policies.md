---
title: ASP.NET Core 中基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序，以在 ASP.NET Core 的应用程序中强制实施授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074157"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="486f2-103">ASP.NET Core 中基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="486f2-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="486f2-104">[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)，都使用了要求、要求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="486f2-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="486f2-105">这些构建基块支持代码中授权评估的表达式。</span><span class="sxs-lookup"><span data-stu-id="486f2-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="486f2-106">结果是一种更丰富的可重复使用的授权结构。</span><span class="sxs-lookup"><span data-stu-id="486f2-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="486f2-107">授权策略包括一个或多个要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="486f2-108">它在授权服务配置的一部分中注册， `Startup.ConfigureServices` 方法如下：</span><span class="sxs-lookup"><span data-stu-id="486f2-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="486f2-109">在前面的示例中，创建了 "AtLeast21" 策略。</span><span class="sxs-lookup"><span data-stu-id="486f2-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="486f2-110">它有一个 &mdash; 最小期限的要求，它作为要求的参数提供。</span><span class="sxs-lookup"><span data-stu-id="486f2-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="486f2-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="486f2-111">IAuthorizationService</span></span> 

<span data-ttu-id="486f2-112">确定授权是否成功的主要服务是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：</span><span class="sxs-lookup"><span data-stu-id="486f2-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="486f2-113">前面的代码突出显示了[IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。</span><span class="sxs-lookup"><span data-stu-id="486f2-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="486f2-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是无方法的标记服务，以及用于跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="486f2-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="486f2-115">每个 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 负责检查是否满足要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="486f2-116">此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 类是处理程序用来标记是否已满足要求的类：</span><span class="sxs-lookup"><span data-stu-id="486f2-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="486f2-117">下面的代码显示授权服务的默认实现（和批注批注）的默认实现：</span><span class="sxs-lookup"><span data-stu-id="486f2-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="486f2-118">下面的代码演示了一个典型的 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="486f2-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="486f2-119">使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 进行授权。</span><span class="sxs-lookup"><span data-stu-id="486f2-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="486f2-120">将策略应用到 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="486f2-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="486f2-121">如果使用的是 Razor 页面，请参阅本文档中的[将策略应用于 Razor 页面](#apply-policies-to-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="486f2-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="486f2-122">策略通过使用 `[Authorize]` 具有策略名称的属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="486f2-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="486f2-123">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="486f2-124">将策略应用到 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="486f2-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="486f2-125">策略是 Razor 通过使用具有策略名称的属性应用于页面的 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="486f2-126">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="486f2-127">还可以 Razor 通过使用[授权约定](xref:security/authorization/razor-pages-authorization)，将策略应用到页面。</span><span class="sxs-lookup"><span data-stu-id="486f2-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="486f2-128">要求</span><span class="sxs-lookup"><span data-stu-id="486f2-128">Requirements</span></span>

<span data-ttu-id="486f2-129">授权要求是一个数据参数集合，策略可以使用这些参数来评估当前用户主体。</span><span class="sxs-lookup"><span data-stu-id="486f2-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="486f2-130">在我们的 "AtLeast21" 策略中，要求是 &mdash; 最小年龄的单个参数。</span><span class="sxs-lookup"><span data-stu-id="486f2-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="486f2-131">要求实现[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，它是一个空的标记接口。</span><span class="sxs-lookup"><span data-stu-id="486f2-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="486f2-132">可以按如下所示实现参数化最低期限要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="486f2-133">如果授权策略包含多个授权要求，则所有要求必须通过，才能成功进行策略评估。</span><span class="sxs-lookup"><span data-stu-id="486f2-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="486f2-134">换句话说，添加到单个授权策略中的多个授权要求**将分别处理**。</span><span class="sxs-lookup"><span data-stu-id="486f2-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="486f2-135">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="486f2-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="486f2-136">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="486f2-136">Authorization handlers</span></span>

<span data-ttu-id="486f2-137">授权处理程序负责计算要求的属性。</span><span class="sxs-lookup"><span data-stu-id="486f2-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="486f2-138">授权处理程序根据提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)评估要求，以确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="486f2-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="486f2-139">要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="486f2-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="486f2-140">处理程序可能会[继承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要处理的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="486f2-141">或者，处理程序可以实现[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="486f2-142">为一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="486f2-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="486f2-143">下面是一种一对一关系的示例，其中最小 age 处理程序利用了单一要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="486f2-144">上述代码确定当前用户主体是否具有由已知的可信颁发者颁发的出生日期。</span><span class="sxs-lookup"><span data-stu-id="486f2-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="486f2-145">当缺少声明时无法进行授权，在这种情况下，将返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="486f2-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="486f2-146">当声明存在时，将计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="486f2-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="486f2-147">如果用户达到了要求所定义的最小时间，则会认为授权成功。</span><span class="sxs-lookup"><span data-stu-id="486f2-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="486f2-148">授权成功后， `context.Succeed` 将通过满足要求作为其唯一参数调用。</span><span class="sxs-lookup"><span data-stu-id="486f2-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="486f2-149">为多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="486f2-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="486f2-150">下面是一个一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="486f2-151">前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 属性，该属性包含未标记为成功的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="486f2-152">对于 `ReadPermission` 要求，用户必须是所有者或主办方才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="486f2-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="486f2-153">对于 `EditPermission` 或 `DeletePermission` 要求，该用户必须是访问所请求资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="486f2-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="486f2-154">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="486f2-154">Handler registration</span></span>

<span data-ttu-id="486f2-155">在配置过程中，将在服务集合中注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="486f2-156">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="486f2-157">前面的代码 `MinimumAgeHandler` 通过调用注册为单一实例 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="486f2-158">可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="486f2-159">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="486f2-159">What should a handler return?</span></span>

<span data-ttu-id="486f2-160">请注意， `Handle` [处理程序示例](#security-authorization-handler-example)中的方法不返回值。</span><span class="sxs-lookup"><span data-stu-id="486f2-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="486f2-161">表示成功或失败的状态如何？</span><span class="sxs-lookup"><span data-stu-id="486f2-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="486f2-162">处理程序通过调用来指示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，同时传递已成功验证的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="486f2-163">处理程序通常不需要处理故障，因为同一要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="486f2-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="486f2-164">若要保证故障，即使其他要求处理程序成功，请调用 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="486f2-165">如果处理程序调用 `context.Succeed` 或 `context.Fail` ，则仍将调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="486f2-166">这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或失败，也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="486f2-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="486f2-167">如果设置为 `false` ，则在调用时， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（ASP.NET Core 在1.1 和更高版本中提供）会将处理程序的执行 `context.Fail` 操作短路。</span><span class="sxs-lookup"><span data-stu-id="486f2-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="486f2-168">`InvokeHandlersAfterFailure`默认为 `true` ，在这种情况下，将调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="486f2-169">即使身份验证失败，也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="486f2-170">为什么需要多个处理程序才能实现要求？</span><span class="sxs-lookup"><span data-stu-id="486f2-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="486f2-171">如果希望计算基于**或**，请为单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="486f2-172">例如，Microsoft 的门只有用密钥卡打开。</span><span class="sxs-lookup"><span data-stu-id="486f2-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="486f2-173">如果在家中保留了密钥卡，则接待员会打印一个临时贴纸，并为您打开门。</span><span class="sxs-lookup"><span data-stu-id="486f2-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="486f2-174">在这种情况下，你将有一个要求*BuildingEntry*，但有多个处理程序，每个处理程序都检查单个需求。</span><span class="sxs-lookup"><span data-stu-id="486f2-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="486f2-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="486f2-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="486f2-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="486f2-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="486f2-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="486f2-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="486f2-178">确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="486f2-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="486f2-179">如果某个处理程序在某一策略评估后成功 `BuildingEntryRequirement` ，则策略评估将成功。</span><span class="sxs-lookup"><span data-stu-id="486f2-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="486f2-180">使用 func 来实现策略</span><span class="sxs-lookup"><span data-stu-id="486f2-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="486f2-181">在某些情况下，实现策略的情况非常简单，只是在代码中表达。</span><span class="sxs-lookup"><span data-stu-id="486f2-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="486f2-182">`Func<AuthorizationHandlerContext, bool>`使用策略生成器配置策略时，可以提供 `RequireAssertion` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="486f2-183">例如， `BadgeEntryHandler` 可以按如下所示重写前面的：</span><span class="sxs-lookup"><span data-stu-id="486f2-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="486f2-184">访问处理程序中的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="486f2-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="486f2-185">`HandleRequirementAsync`在授权处理程序中实现的方法具有两个参数： `AuthorizationHandlerContext` 和 `TRequirement` 正在处理的。</span><span class="sxs-lookup"><span data-stu-id="486f2-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="486f2-186">诸如 MVC 或的框架可 SignalR 自由地将任何对象添加到 `Resource` 上的属性 `AuthorizationHandlerContext` ，以传递附加信息。</span><span class="sxs-lookup"><span data-stu-id="486f2-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="486f2-187">使用终结点路由时，授权通常由授权中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="486f2-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="486f2-188">在这种情况下， `Resource` 属性为的实例 <xref:Microsoft.AspNetCore.Http.Endpoint> 。</span><span class="sxs-lookup"><span data-stu-id="486f2-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="486f2-189">终结点可用于探测要路由到的基础资源。</span><span class="sxs-lookup"><span data-stu-id="486f2-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="486f2-190">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="486f2-191">终结点不提供对当前的访问 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="486f2-192">使用终结点路由时，使用在 `IHttpContextAcessor` `HttpContext` 授权处理程序内进行访问。</span><span class="sxs-lookup"><span data-stu-id="486f2-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="486f2-193">有关详细信息，请参阅[使用来自自定义组件的 HttpContext](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)。</span><span class="sxs-lookup"><span data-stu-id="486f2-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="486f2-194">对于传统路由，或在 MVC 的授权筛选器中进行授权时，的值 `Resource` 为 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 实例。</span><span class="sxs-lookup"><span data-stu-id="486f2-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="486f2-195">此属性提供对 `HttpContext` 、以及 `RouteData` MVC 和页面提供的其他内容的访问 Razor 。</span><span class="sxs-lookup"><span data-stu-id="486f2-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="486f2-196">使用 `Resource` 属性是特定于框架的。</span><span class="sxs-lookup"><span data-stu-id="486f2-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="486f2-197">使用属性中的信息会将 `Resource` 授权策略限制为特定框架。</span><span class="sxs-lookup"><span data-stu-id="486f2-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="486f2-198">应 `Resource` 使用关键字强制转换属性 `is` ，然后确认强制转换已成功，以确保在 `InvalidCastException` 其他框架上运行时代码不会崩溃：</span><span class="sxs-lookup"><span data-stu-id="486f2-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="486f2-199">[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)，都使用了要求、要求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="486f2-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="486f2-200">这些构建基块支持代码中授权评估的表达式。</span><span class="sxs-lookup"><span data-stu-id="486f2-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="486f2-201">结果是一种更丰富的可重复使用的授权结构。</span><span class="sxs-lookup"><span data-stu-id="486f2-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="486f2-202">授权策略包括一个或多个要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="486f2-203">它在授权服务配置的一部分中注册， `Startup.ConfigureServices` 方法如下：</span><span class="sxs-lookup"><span data-stu-id="486f2-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="486f2-204">在前面的示例中，创建了 "AtLeast21" 策略。</span><span class="sxs-lookup"><span data-stu-id="486f2-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="486f2-205">它有一个 &mdash; 最小期限的要求，它作为要求的参数提供。</span><span class="sxs-lookup"><span data-stu-id="486f2-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="486f2-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="486f2-206">IAuthorizationService</span></span> 

<span data-ttu-id="486f2-207">确定授权是否成功的主要服务是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：</span><span class="sxs-lookup"><span data-stu-id="486f2-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="486f2-208">前面的代码突出显示了[IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。</span><span class="sxs-lookup"><span data-stu-id="486f2-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="486f2-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是无方法的标记服务，以及用于跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="486f2-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="486f2-210">每个 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 负责检查是否满足要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="486f2-211">此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 类是处理程序用来标记是否已满足要求的类：</span><span class="sxs-lookup"><span data-stu-id="486f2-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="486f2-212">下面的代码显示授权服务的默认实现（和批注批注）的默认实现：</span><span class="sxs-lookup"><span data-stu-id="486f2-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="486f2-213">下面的代码演示了一个典型的 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="486f2-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="486f2-214">使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 进行授权。</span><span class="sxs-lookup"><span data-stu-id="486f2-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="486f2-215">将策略应用到 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="486f2-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="486f2-216">如果使用的是 Razor 页面，请参阅本文档中的[将策略应用于 Razor 页面](#apply-policies-to-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="486f2-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="486f2-217">策略通过使用 `[Authorize]` 具有策略名称的属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="486f2-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="486f2-218">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="486f2-219">将策略应用到 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="486f2-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="486f2-220">策略是 Razor 通过使用具有策略名称的属性应用于页面的 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="486f2-221">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="486f2-222">还可以 Razor 通过使用[授权约定](xref:security/authorization/razor-pages-authorization)，将策略应用到页面。</span><span class="sxs-lookup"><span data-stu-id="486f2-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="486f2-223">要求</span><span class="sxs-lookup"><span data-stu-id="486f2-223">Requirements</span></span>

<span data-ttu-id="486f2-224">授权要求是一个数据参数集合，策略可以使用这些参数来评估当前用户主体。</span><span class="sxs-lookup"><span data-stu-id="486f2-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="486f2-225">在我们的 "AtLeast21" 策略中，要求是 &mdash; 最小年龄的单个参数。</span><span class="sxs-lookup"><span data-stu-id="486f2-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="486f2-226">要求实现[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，它是一个空的标记接口。</span><span class="sxs-lookup"><span data-stu-id="486f2-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="486f2-227">可以按如下所示实现参数化最低期限要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="486f2-228">如果授权策略包含多个授权要求，则所有要求必须通过，才能成功进行策略评估。</span><span class="sxs-lookup"><span data-stu-id="486f2-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="486f2-229">换句话说，添加到单个授权策略中的多个授权要求**将分别处理**。</span><span class="sxs-lookup"><span data-stu-id="486f2-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="486f2-230">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="486f2-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="486f2-231">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="486f2-231">Authorization handlers</span></span>

<span data-ttu-id="486f2-232">授权处理程序负责计算要求的属性。</span><span class="sxs-lookup"><span data-stu-id="486f2-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="486f2-233">授权处理程序根据提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)评估要求，以确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="486f2-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="486f2-234">要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="486f2-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="486f2-235">处理程序可能会[继承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要处理的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="486f2-236">或者，处理程序可以实现[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="486f2-237">为一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="486f2-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="486f2-238">下面是一种一对一关系的示例，其中最小 age 处理程序利用了单一要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="486f2-239">上述代码确定当前用户主体是否具有由已知的可信颁发者颁发的出生日期。</span><span class="sxs-lookup"><span data-stu-id="486f2-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="486f2-240">当缺少声明时无法进行授权，在这种情况下，将返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="486f2-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="486f2-241">当声明存在时，将计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="486f2-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="486f2-242">如果用户达到了要求所定义的最小时间，则会认为授权成功。</span><span class="sxs-lookup"><span data-stu-id="486f2-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="486f2-243">授权成功后， `context.Succeed` 将通过满足要求作为其唯一参数调用。</span><span class="sxs-lookup"><span data-stu-id="486f2-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="486f2-244">为多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="486f2-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="486f2-245">下面是一个一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：</span><span class="sxs-lookup"><span data-stu-id="486f2-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="486f2-246">前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 属性，该属性包含未标记为成功的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="486f2-247">对于 `ReadPermission` 要求，用户必须是所有者或主办方才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="486f2-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="486f2-248">对于 `EditPermission` 或 `DeletePermission` 要求，该用户必须是访问所请求资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="486f2-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="486f2-249">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="486f2-249">Handler registration</span></span>

<span data-ttu-id="486f2-250">在配置过程中，将在服务集合中注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="486f2-251">例如：</span><span class="sxs-lookup"><span data-stu-id="486f2-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="486f2-252">前面的代码 `MinimumAgeHandler` 通过调用注册为单一实例 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="486f2-253">可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="486f2-254">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="486f2-254">What should a handler return?</span></span>

<span data-ttu-id="486f2-255">请注意， `Handle` [处理程序示例](#security-authorization-handler-example)中的方法不返回值。</span><span class="sxs-lookup"><span data-stu-id="486f2-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="486f2-256">表示成功或失败的状态如何？</span><span class="sxs-lookup"><span data-stu-id="486f2-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="486f2-257">处理程序通过调用来指示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，同时传递已成功验证的要求。</span><span class="sxs-lookup"><span data-stu-id="486f2-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="486f2-258">处理程序通常不需要处理故障，因为同一要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="486f2-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="486f2-259">若要保证故障，即使其他要求处理程序成功，请调用 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="486f2-260">如果处理程序调用 `context.Succeed` 或 `context.Fail` ，则仍将调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="486f2-261">这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或失败，也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="486f2-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="486f2-262">如果设置为 `false` ，则在调用时， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（ASP.NET Core 在1.1 和更高版本中提供）会将处理程序的执行 `context.Fail` 操作短路。</span><span class="sxs-lookup"><span data-stu-id="486f2-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="486f2-263">`InvokeHandlersAfterFailure`默认为 `true` ，在这种情况下，将调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="486f2-264">即使身份验证失败，也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="486f2-265">为什么需要多个处理程序才能实现要求？</span><span class="sxs-lookup"><span data-stu-id="486f2-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="486f2-266">如果希望计算基于**或**，请为单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="486f2-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="486f2-267">例如，Microsoft 的门只有用密钥卡打开。</span><span class="sxs-lookup"><span data-stu-id="486f2-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="486f2-268">如果在家中保留了密钥卡，则接待员会打印一个临时贴纸，并为您打开门。</span><span class="sxs-lookup"><span data-stu-id="486f2-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="486f2-269">在这种情况下，你将有一个要求*BuildingEntry*，但有多个处理程序，每个处理程序都检查单个需求。</span><span class="sxs-lookup"><span data-stu-id="486f2-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="486f2-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="486f2-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="486f2-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="486f2-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="486f2-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="486f2-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="486f2-273">确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="486f2-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="486f2-274">如果某个处理程序在某一策略评估后成功 `BuildingEntryRequirement` ，则策略评估将成功。</span><span class="sxs-lookup"><span data-stu-id="486f2-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="486f2-275">使用 func 来实现策略</span><span class="sxs-lookup"><span data-stu-id="486f2-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="486f2-276">在某些情况下，实现策略的情况非常简单，只是在代码中表达。</span><span class="sxs-lookup"><span data-stu-id="486f2-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="486f2-277">`Func<AuthorizationHandlerContext, bool>`使用策略生成器配置策略时，可以提供 `RequireAssertion` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="486f2-278">例如， `BadgeEntryHandler` 可以按如下所示重写前面的：</span><span class="sxs-lookup"><span data-stu-id="486f2-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="486f2-279">访问处理程序中的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="486f2-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="486f2-280">`HandleRequirementAsync`在授权处理程序中实现的方法具有两个参数： `AuthorizationHandlerContext` 和 `TRequirement` 正在处理的。</span><span class="sxs-lookup"><span data-stu-id="486f2-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="486f2-281">诸如 MVC 或的框架可 SignalR 自由地将任何对象添加到 `Resource` 上的属性 `AuthorizationHandlerContext` ，以传递附加信息。</span><span class="sxs-lookup"><span data-stu-id="486f2-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="486f2-282">例如，MVC 在属性中传递[AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)的实例 `Resource` 。</span><span class="sxs-lookup"><span data-stu-id="486f2-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="486f2-283">此属性提供对 `HttpContext` 、以及 `RouteData` MVC 和页面提供的其他内容的访问 Razor 。</span><span class="sxs-lookup"><span data-stu-id="486f2-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="486f2-284">使用 `Resource` 属性是特定于框架的。</span><span class="sxs-lookup"><span data-stu-id="486f2-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="486f2-285">使用属性中的信息会将 `Resource` 授权策略限制为特定框架。</span><span class="sxs-lookup"><span data-stu-id="486f2-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="486f2-286">应 `Resource` 使用关键字强制转换属性 `is` ，然后确认强制转换已成功，以确保在 `InvalidCastException` 其他框架上运行时代码不会崩溃：</span><span class="sxs-lookup"><span data-stu-id="486f2-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
