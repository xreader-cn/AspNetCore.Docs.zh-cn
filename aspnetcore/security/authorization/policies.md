---
title: ASP.NET Core 中基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序，以在 ASP.NET Core 的应用程序中强制实施授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 03d6e7fdc4ab4b5e4925508952bfd6c835d90486
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021271"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="fc84e-103">ASP.NET Core 中基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="fc84e-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fc84e-104">[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)，都使用了要求、要求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="fc84e-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="fc84e-105">这些构建基块支持代码中授权评估的表达式。</span><span class="sxs-lookup"><span data-stu-id="fc84e-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="fc84e-106">结果是一种更丰富的可重复使用的授权结构。</span><span class="sxs-lookup"><span data-stu-id="fc84e-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="fc84e-107">授权策略包括一个或多个要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="fc84e-108">它在授权服务配置的一部分中注册， `Startup.ConfigureServices` 方法如下：</span><span class="sxs-lookup"><span data-stu-id="fc84e-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="fc84e-109">在前面的示例中，创建了 "AtLeast21" 策略。</span><span class="sxs-lookup"><span data-stu-id="fc84e-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="fc84e-110">它有一个 &mdash; 最小期限的要求，它作为要求的参数提供。</span><span class="sxs-lookup"><span data-stu-id="fc84e-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="fc84e-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="fc84e-111">IAuthorizationService</span></span> 

<span data-ttu-id="fc84e-112">确定授权是否成功的主要服务是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：</span><span class="sxs-lookup"><span data-stu-id="fc84e-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="fc84e-113">前面的代码突出显示了[IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。</span><span class="sxs-lookup"><span data-stu-id="fc84e-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="fc84e-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是无方法的标记服务，以及用于跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="fc84e-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="fc84e-115">每个 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 负责检查是否满足要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="fc84e-116">此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 类是处理程序用来标记是否已满足要求的类：</span><span class="sxs-lookup"><span data-stu-id="fc84e-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="fc84e-117">以下代码显示了简化的 (，并注释了授权服务的默认实现) 注释：</span><span class="sxs-lookup"><span data-stu-id="fc84e-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="fc84e-118">下面的代码演示了一个典型的 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fc84e-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="fc84e-119">使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 进行授权。</span><span class="sxs-lookup"><span data-stu-id="fc84e-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="fc84e-120">将策略应用到 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="fc84e-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="fc84e-121">如果使用的是 Razor 页面，请参阅本文档中的[将策略应用于 Razor 页面](#apply-policies-to-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="fc84e-122">策略通过使用 `[Authorize]` 具有策略名称的属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="fc84e-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="fc84e-123">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="fc84e-124">将策略应用到 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="fc84e-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="fc84e-125">策略是 Razor 通过使用具有策略名称的属性应用于页面的 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="fc84e-126">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="fc84e-127">策略***不***能应用于 Razor 页面处理程序级别，它们必须应用于页面。</span><span class="sxs-lookup"><span data-stu-id="fc84e-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="fc84e-128">可以 Razor 通过使用[授权约定](xref:security/authorization/razor-pages-authorization)将策略应用于页面。</span><span class="sxs-lookup"><span data-stu-id="fc84e-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="fc84e-129">要求</span><span class="sxs-lookup"><span data-stu-id="fc84e-129">Requirements</span></span>

<span data-ttu-id="fc84e-130">授权要求是一个数据参数集合，策略可以使用这些参数来评估当前用户主体。</span><span class="sxs-lookup"><span data-stu-id="fc84e-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="fc84e-131">在我们的 "AtLeast21" 策略中，要求是 &mdash; 最小年龄的单个参数。</span><span class="sxs-lookup"><span data-stu-id="fc84e-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="fc84e-132">要求实现[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，它是一个空的标记接口。</span><span class="sxs-lookup"><span data-stu-id="fc84e-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="fc84e-133">可以按如下所示实现参数化最低期限要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="fc84e-134">如果授权策略包含多个授权要求，则所有要求必须通过，才能成功进行策略评估。</span><span class="sxs-lookup"><span data-stu-id="fc84e-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="fc84e-135">换句话说，添加到单个授权策略中的多个授权要求**将分别处理**。</span><span class="sxs-lookup"><span data-stu-id="fc84e-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="fc84e-136">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="fc84e-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="fc84e-137">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="fc84e-137">Authorization handlers</span></span>

<span data-ttu-id="fc84e-138">授权处理程序负责计算要求的属性。</span><span class="sxs-lookup"><span data-stu-id="fc84e-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="fc84e-139">授权处理程序根据提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)评估要求，以确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="fc84e-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="fc84e-140">要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="fc84e-141">处理程序可能会[继承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要处理的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="fc84e-142">或者，处理程序可以实现[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="fc84e-143">为一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="fc84e-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="fc84e-144">下面是一种一对一关系的示例，其中最小 age 处理程序利用了单一要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="fc84e-145">上述代码确定当前用户主体是否具有由已知的可信颁发者颁发的出生日期。</span><span class="sxs-lookup"><span data-stu-id="fc84e-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="fc84e-146">当缺少声明时无法进行授权，在这种情况下，将返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="fc84e-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="fc84e-147">当声明存在时，将计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="fc84e-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="fc84e-148">如果用户达到了要求所定义的最小时间，则会认为授权成功。</span><span class="sxs-lookup"><span data-stu-id="fc84e-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="fc84e-149">授权成功后， `context.Succeed` 将通过满足要求作为其唯一参数调用。</span><span class="sxs-lookup"><span data-stu-id="fc84e-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="fc84e-150">为多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="fc84e-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="fc84e-151">下面是一个一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="fc84e-152">前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 属性，该属性包含未标记为成功的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="fc84e-153">对于 `ReadPermission` 要求，用户必须是所有者或主办方才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="fc84e-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="fc84e-154">对于 `EditPermission` 或 `DeletePermission` 要求，该用户必须是访问所请求资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="fc84e-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="fc84e-155">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="fc84e-155">Handler registration</span></span>

<span data-ttu-id="fc84e-156">在配置过程中，将在服务集合中注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="fc84e-157">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="fc84e-158">前面的代码 `MinimumAgeHandler` 通过调用注册为单一实例 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="fc84e-159">可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="fc84e-160">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="fc84e-160">What should a handler return?</span></span>

<span data-ttu-id="fc84e-161">请注意， `Handle` [处理程序示例](#security-authorization-handler-example)中的方法不返回值。</span><span class="sxs-lookup"><span data-stu-id="fc84e-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="fc84e-162">表示成功或失败的状态如何？</span><span class="sxs-lookup"><span data-stu-id="fc84e-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="fc84e-163">处理程序通过调用来指示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，同时传递已成功验证的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="fc84e-164">处理程序通常不需要处理故障，因为同一要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="fc84e-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="fc84e-165">若要保证故障，即使其他要求处理程序成功，请调用 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="fc84e-166">如果处理程序调用 `context.Succeed` 或 `context.Fail` ，则仍将调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="fc84e-167">这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或失败，也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="fc84e-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="fc84e-168">如果设置为 `false` ，则在调用时， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性 (可用于 ASP.NET Core 1.1 及更高版本) 短线路执行处理程序 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="fc84e-169">`InvokeHandlersAfterFailure`默认为 `true` ，在这种情况下，将调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="fc84e-170">即使身份验证失败，也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="fc84e-171">为什么需要多个处理程序才能实现要求？</span><span class="sxs-lookup"><span data-stu-id="fc84e-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="fc84e-172">如果希望计算基于**或**，请为单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="fc84e-173">例如，Microsoft 的门只有用密钥卡打开。</span><span class="sxs-lookup"><span data-stu-id="fc84e-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="fc84e-174">如果在家中保留了密钥卡，则接待员会打印一个临时贴纸，并为您打开门。</span><span class="sxs-lookup"><span data-stu-id="fc84e-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="fc84e-175">在这种情况下，你将有一个要求*BuildingEntry*，但有多个处理程序，每个处理程序都检查单个需求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="fc84e-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="fc84e-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="fc84e-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="fc84e-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="fc84e-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="fc84e-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="fc84e-179">确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="fc84e-180">如果某个处理程序在某一策略评估后成功 `BuildingEntryRequirement` ，则策略评估将成功。</span><span class="sxs-lookup"><span data-stu-id="fc84e-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="fc84e-181">使用 func 来实现策略</span><span class="sxs-lookup"><span data-stu-id="fc84e-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="fc84e-182">在某些情况下，实现策略的情况非常简单，只是在代码中表达。</span><span class="sxs-lookup"><span data-stu-id="fc84e-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="fc84e-183">`Func<AuthorizationHandlerContext, bool>`使用策略生成器配置策略时，可以提供 `RequireAssertion` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="fc84e-184">例如， `BadgeEntryHandler` 可以按如下所示重写前面的：</span><span class="sxs-lookup"><span data-stu-id="fc84e-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="fc84e-185">访问处理程序中的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="fc84e-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="fc84e-186">`HandleRequirementAsync`在授权处理程序中实现的方法具有两个参数： `AuthorizationHandlerContext` 和 `TRequirement` 正在处理的。</span><span class="sxs-lookup"><span data-stu-id="fc84e-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="fc84e-187">诸如 MVC 或的框架可 SignalR 自由地将任何对象添加到 `Resource` 上的属性 `AuthorizationHandlerContext` ，以传递附加信息。</span><span class="sxs-lookup"><span data-stu-id="fc84e-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="fc84e-188">使用终结点路由时，授权通常由授权中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="fc84e-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="fc84e-189">在这种情况下， `Resource` 属性为的实例 <xref:Microsoft.AspNetCore.Http.Endpoint> 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="fc84e-190">终结点可用于探测要路由到的基础资源。</span><span class="sxs-lookup"><span data-stu-id="fc84e-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="fc84e-191">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="fc84e-192">终结点不提供对当前的访问 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="fc84e-193">使用终结点路由时，使用在 `IHttpContextAcessor` `HttpContext` 授权处理程序内进行访问。</span><span class="sxs-lookup"><span data-stu-id="fc84e-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="fc84e-194">有关详细信息，请参阅[使用来自自定义组件的 HttpContext](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="fc84e-195">对于传统路由，或在 MVC 的授权筛选器中进行授权时，的值 `Resource` 为 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 实例。</span><span class="sxs-lookup"><span data-stu-id="fc84e-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="fc84e-196">此属性提供对 `HttpContext` 、以及 `RouteData` MVC 和页面提供的其他内容的访问 Razor 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="fc84e-197">使用 `Resource` 属性是特定于框架的。</span><span class="sxs-lookup"><span data-stu-id="fc84e-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="fc84e-198">使用属性中的信息会将 `Resource` 授权策略限制为特定框架。</span><span class="sxs-lookup"><span data-stu-id="fc84e-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="fc84e-199">应 `Resource` 使用关键字强制转换属性 `is` ，然后确认强制转换已成功，以确保在 `InvalidCastException` 其他框架上运行时代码不会崩溃：</span><span class="sxs-lookup"><span data-stu-id="fc84e-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="fc84e-200">全局要求对所有用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="fc84e-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fc84e-201">[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)，都使用了要求、要求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="fc84e-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="fc84e-202">这些构建基块支持代码中授权评估的表达式。</span><span class="sxs-lookup"><span data-stu-id="fc84e-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="fc84e-203">结果是一种更丰富的可重复使用的授权结构。</span><span class="sxs-lookup"><span data-stu-id="fc84e-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="fc84e-204">授权策略包括一个或多个要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="fc84e-205">它在授权服务配置的一部分中注册， `Startup.ConfigureServices` 方法如下：</span><span class="sxs-lookup"><span data-stu-id="fc84e-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="fc84e-206">在前面的示例中，创建了 "AtLeast21" 策略。</span><span class="sxs-lookup"><span data-stu-id="fc84e-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="fc84e-207">它有一个 &mdash; 最小期限的要求，它作为要求的参数提供。</span><span class="sxs-lookup"><span data-stu-id="fc84e-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="fc84e-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="fc84e-208">IAuthorizationService</span></span> 

<span data-ttu-id="fc84e-209">确定授权是否成功的主要服务是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ：</span><span class="sxs-lookup"><span data-stu-id="fc84e-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="fc84e-210">前面的代码突出显示了[IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。</span><span class="sxs-lookup"><span data-stu-id="fc84e-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="fc84e-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是无方法的标记服务，以及用于跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="fc84e-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="fc84e-212">每个 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 负责检查是否满足要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="fc84e-213">此 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 类是处理程序用来标记是否已满足要求的类：</span><span class="sxs-lookup"><span data-stu-id="fc84e-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="fc84e-214">以下代码显示了简化的 (，并注释了授权服务的默认实现) 注释：</span><span class="sxs-lookup"><span data-stu-id="fc84e-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="fc84e-215">下面的代码演示了一个典型的 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fc84e-215">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="fc84e-216">使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 进行授权。</span><span class="sxs-lookup"><span data-stu-id="fc84e-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="fc84e-217">将策略应用到 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="fc84e-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="fc84e-218">如果使用的是 Razor 页面，请参阅本文档中的[将策略应用于 Razor 页面](#apply-policies-to-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="fc84e-219">策略通过使用 `[Authorize]` 具有策略名称的属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="fc84e-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="fc84e-220">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="fc84e-221">将策略应用到 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="fc84e-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="fc84e-222">策略是 Razor 通过使用具有策略名称的属性应用于页面的 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="fc84e-223">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="fc84e-224">还可以 Razor 通过使用[授权约定](xref:security/authorization/razor-pages-authorization)，将策略应用到页面。</span><span class="sxs-lookup"><span data-stu-id="fc84e-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="fc84e-225">要求</span><span class="sxs-lookup"><span data-stu-id="fc84e-225">Requirements</span></span>

<span data-ttu-id="fc84e-226">授权要求是一个数据参数集合，策略可以使用这些参数来评估当前用户主体。</span><span class="sxs-lookup"><span data-stu-id="fc84e-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="fc84e-227">在我们的 "AtLeast21" 策略中，要求是 &mdash; 最小年龄的单个参数。</span><span class="sxs-lookup"><span data-stu-id="fc84e-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="fc84e-228">要求实现[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，它是一个空的标记接口。</span><span class="sxs-lookup"><span data-stu-id="fc84e-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="fc84e-229">可以按如下所示实现参数化最低期限要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="fc84e-230">如果授权策略包含多个授权要求，则所有要求必须通过，才能成功进行策略评估。</span><span class="sxs-lookup"><span data-stu-id="fc84e-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="fc84e-231">换句话说，添加到单个授权策略中的多个授权要求**将分别处理**。</span><span class="sxs-lookup"><span data-stu-id="fc84e-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="fc84e-232">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="fc84e-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="fc84e-233">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="fc84e-233">Authorization handlers</span></span>

<span data-ttu-id="fc84e-234">授权处理程序负责计算要求的属性。</span><span class="sxs-lookup"><span data-stu-id="fc84e-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="fc84e-235">授权处理程序根据提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)评估要求，以确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="fc84e-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="fc84e-236">要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="fc84e-237">处理程序可能会[继承 \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是要处理的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="fc84e-238">或者，处理程序可以实现[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="fc84e-239">为一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="fc84e-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="fc84e-240">下面是一种一对一关系的示例，其中最小 age 处理程序利用了单一要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="fc84e-241">上述代码确定当前用户主体是否具有由已知的可信颁发者颁发的出生日期。</span><span class="sxs-lookup"><span data-stu-id="fc84e-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="fc84e-242">当缺少声明时无法进行授权，在这种情况下，将返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="fc84e-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="fc84e-243">当声明存在时，将计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="fc84e-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="fc84e-244">如果用户达到了要求所定义的最小时间，则会认为授权成功。</span><span class="sxs-lookup"><span data-stu-id="fc84e-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="fc84e-245">授权成功后， `context.Succeed` 将通过满足要求作为其唯一参数调用。</span><span class="sxs-lookup"><span data-stu-id="fc84e-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="fc84e-246">为多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="fc84e-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="fc84e-247">下面是一个一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：</span><span class="sxs-lookup"><span data-stu-id="fc84e-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="fc84e-248">前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 属性，该属性包含未标记为成功的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="fc84e-249">对于 `ReadPermission` 要求，用户必须是所有者或主办方才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="fc84e-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="fc84e-250">对于 `EditPermission` 或 `DeletePermission` 要求，该用户必须是访问所请求资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="fc84e-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="fc84e-251">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="fc84e-251">Handler registration</span></span>

<span data-ttu-id="fc84e-252">在配置过程中，将在服务集合中注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="fc84e-253">例如：</span><span class="sxs-lookup"><span data-stu-id="fc84e-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="fc84e-254">前面的代码 `MinimumAgeHandler` 通过调用注册为单一实例 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="fc84e-255">可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="fc84e-256">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="fc84e-256">What should a handler return?</span></span>

<span data-ttu-id="fc84e-257">请注意， `Handle` [处理程序示例](#security-authorization-handler-example)中的方法不返回值。</span><span class="sxs-lookup"><span data-stu-id="fc84e-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="fc84e-258">表示成功或失败的状态如何？</span><span class="sxs-lookup"><span data-stu-id="fc84e-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="fc84e-259">处理程序通过调用来指示成功 `context.Succeed(IAuthorizationRequirement requirement)` ，同时传递已成功验证的要求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="fc84e-260">处理程序通常不需要处理故障，因为同一要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="fc84e-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="fc84e-261">若要保证故障，即使其他要求处理程序成功，请调用 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="fc84e-262">如果处理程序调用 `context.Succeed` 或 `context.Fail` ，则仍将调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="fc84e-263">这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或失败，也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="fc84e-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="fc84e-264">如果设置为 `false` ，则在调用时， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性 (可用于 ASP.NET Core 1.1 及更高版本) 短线路执行处理程序 `context.Fail` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="fc84e-265">`InvokeHandlersAfterFailure`默认为 `true` ，在这种情况下，将调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="fc84e-266">即使身份验证失败，也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="fc84e-267">为什么需要多个处理程序才能实现要求？</span><span class="sxs-lookup"><span data-stu-id="fc84e-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="fc84e-268">如果希望计算基于**或**，请为单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="fc84e-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="fc84e-269">例如，Microsoft 的门只有用密钥卡打开。</span><span class="sxs-lookup"><span data-stu-id="fc84e-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="fc84e-270">如果在家中保留了密钥卡，则接待员会打印一个临时贴纸，并为您打开门。</span><span class="sxs-lookup"><span data-stu-id="fc84e-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="fc84e-271">在这种情况下，你将有一个要求*BuildingEntry*，但有多个处理程序，每个处理程序都检查单个需求。</span><span class="sxs-lookup"><span data-stu-id="fc84e-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="fc84e-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="fc84e-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="fc84e-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="fc84e-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="fc84e-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="fc84e-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="fc84e-275">确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="fc84e-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="fc84e-276">如果某个处理程序在某一策略评估后成功 `BuildingEntryRequirement` ，则策略评估将成功。</span><span class="sxs-lookup"><span data-stu-id="fc84e-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="fc84e-277">使用 func 来实现策略</span><span class="sxs-lookup"><span data-stu-id="fc84e-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="fc84e-278">在某些情况下，实现策略的情况非常简单，只是在代码中表达。</span><span class="sxs-lookup"><span data-stu-id="fc84e-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="fc84e-279">`Func<AuthorizationHandlerContext, bool>`使用策略生成器配置策略时，可以提供 `RequireAssertion` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="fc84e-280">例如， `BadgeEntryHandler` 可以按如下所示重写前面的：</span><span class="sxs-lookup"><span data-stu-id="fc84e-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="fc84e-281">访问处理程序中的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="fc84e-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="fc84e-282">`HandleRequirementAsync`在授权处理程序中实现的方法具有两个参数： `AuthorizationHandlerContext` 和 `TRequirement` 正在处理的。</span><span class="sxs-lookup"><span data-stu-id="fc84e-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="fc84e-283">诸如 MVC 或的框架可 SignalR 自由地将任何对象添加到 `Resource` 上的属性 `AuthorizationHandlerContext` ，以传递附加信息。</span><span class="sxs-lookup"><span data-stu-id="fc84e-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="fc84e-284">例如，MVC 在属性中传递[AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)的实例 `Resource` 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="fc84e-285">此属性提供对 `HttpContext` 、以及 `RouteData` MVC 和页面提供的其他内容的访问 Razor 。</span><span class="sxs-lookup"><span data-stu-id="fc84e-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="fc84e-286">使用 `Resource` 属性是特定于框架的。</span><span class="sxs-lookup"><span data-stu-id="fc84e-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="fc84e-287">使用属性中的信息会将 `Resource` 授权策略限制为特定框架。</span><span class="sxs-lookup"><span data-stu-id="fc84e-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="fc84e-288">应 `Resource` 使用关键字强制转换属性 `is` ，然后确认强制转换已成功，以确保在 `InvalidCastException` 其他框架上运行时代码不会崩溃：</span><span class="sxs-lookup"><span data-stu-id="fc84e-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
