---
title: ASP.NET Core中基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序, 以在 ASP.NET Core 的应用程序中强制实施授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: 60625944d4ba31da6b98bdf947991088dc75ed87
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68669973"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="66fe6-103">ASP.NET Core中基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="66fe6-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="66fe6-104">实际上，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)都使用要求、要求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="66fe6-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="66fe6-105">这些构建基块支持在代码中使用授权评估表达式。</span><span class="sxs-lookup"><span data-stu-id="66fe6-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="66fe6-106">结果就是，授权结构更加丰富，可重复使用，并且可以测试。</span><span class="sxs-lookup"><span data-stu-id="66fe6-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="66fe6-107">授权策略包含一个或多个要求。</span><span class="sxs-lookup"><span data-stu-id="66fe6-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="66fe6-108">授权策略包含一个或多个要求，并在 `Startup.ConfigureServices` 方法中作为授权服务配置的一部分注册：</span><span class="sxs-lookup"><span data-stu-id="66fe6-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="66fe6-109">在前面的示例中，创建了一个“AtLeast21”策略。</span><span class="sxs-lookup"><span data-stu-id="66fe6-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="66fe6-110">它只有一个要求&mdash;，即最低年龄，以参数的形式传递给要求。</span><span class="sxs-lookup"><span data-stu-id="66fe6-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="66fe6-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="66fe6-111">IAuthorizationService</span></span> 

<span data-ttu-id="66fe6-112">确定授权是否成功的主要服务是<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="66fe6-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="66fe6-113">前面的代码突出显示了[IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。</span><span class="sxs-lookup"><span data-stu-id="66fe6-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="66fe6-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是无方法的标记服务, 以及用于跟踪授权是否成功的机制。</span><span class="sxs-lookup"><span data-stu-id="66fe6-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="66fe6-115">每<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>个负责检查是否满足要求:</span><span class="sxs-lookup"><span data-stu-id="66fe6-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<span data-ttu-id="66fe6-116">此<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>类是处理程序用来标记是否已满足要求的类:</span><span class="sxs-lookup"><span data-stu-id="66fe6-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="66fe6-117">下面的代码显示授权服务的默认实现 (和批注批注) 的默认实现:</span><span class="sxs-lookup"><span data-stu-id="66fe6-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="66fe6-118">下面的代码演示了一个`ConfigureServices`典型的:</span><span class="sxs-lookup"><span data-stu-id="66fe6-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="66fe6-119">使用<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或`[Authorize(Policy = "Something")]`进行授权。</span><span class="sxs-lookup"><span data-stu-id="66fe6-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="66fe6-120">将策略应用到 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="66fe6-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="66fe6-121">如果使用 Razor Pages, 请参阅本文档中的[将策略应用于 Razor Pages](#applying-policies-to-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="66fe6-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="66fe6-122">策略通过使用`[Authorize]`具有策略名称的属性应用到控制器。</span><span class="sxs-lookup"><span data-stu-id="66fe6-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="66fe6-123">例如：</span><span class="sxs-lookup"><span data-stu-id="66fe6-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="66fe6-124">将策略应用到 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66fe6-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="66fe6-125">通过将`[Authorize]`属性与策略名称一起使用, 将策略应用到 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="66fe6-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="66fe6-126">例如：</span><span class="sxs-lookup"><span data-stu-id="66fe6-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="66fe6-127">还可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization), 将策略应用到 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="66fe6-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="66fe6-128">要求</span><span class="sxs-lookup"><span data-stu-id="66fe6-128">Requirements</span></span>

<span data-ttu-id="66fe6-129">授权要求是一个可供策略用来评估当前用户主体的数据参数的集合。</span><span class="sxs-lookup"><span data-stu-id="66fe6-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="66fe6-130">在我们的“AtLeast21”策略中，此要求是单个参数&mdash;最低年龄。</span><span class="sxs-lookup"><span data-stu-id="66fe6-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="66fe6-131">要求可以实现 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。</span><span class="sxs-lookup"><span data-stu-id="66fe6-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="66fe6-132">参数化的最低年龄要求可以按如下方式实现：</span><span class="sxs-lookup"><span data-stu-id="66fe6-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="66fe6-133">如果授权策略包含多个授权要求, 则所有要求必须通过, 才能成功进行策略评估。</span><span class="sxs-lookup"><span data-stu-id="66fe6-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="66fe6-134">换句话说, 添加到单个授权策略中的多个授权要求将分别处理。</span><span class="sxs-lookup"><span data-stu-id="66fe6-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="66fe6-135">要求不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="66fe6-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="66fe6-136">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="66fe6-136">Authorization handlers</span></span>

<span data-ttu-id="66fe6-137">授权处理程序负责评估要求的属性。</span><span class="sxs-lookup"><span data-stu-id="66fe6-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="66fe6-138">授权处理程序会针对提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 来评估要求，确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="66fe6-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="66fe6-139">一项要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="66fe6-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="66fe6-140">处理程序可以继承 [AuthorizationHandler\<<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中的 `TRequirement` 是需处理的要求。</span><span class="sxs-lookup"><span data-stu-id="66fe6-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="66fe6-141">另外，一个处理程序也可以通过实现 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 来处理多个类型的要求。</span><span class="sxs-lookup"><span data-stu-id="66fe6-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="66fe6-142">为一个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="66fe6-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="66fe6-143">下面是一对一关系的示例，其中的单个最低年龄要求处理程序使用单个要求：</span><span class="sxs-lookup"><span data-stu-id="66fe6-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="66fe6-144">前面的代码确定当前的用户主体是否有一个由已知的受信任颁发者颁发的出生日期声明。</span><span class="sxs-lookup"><span data-stu-id="66fe6-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="66fe6-145">缺少声明时，无法进行授权，这种情况下会返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="66fe6-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="66fe6-146">存在声明时，会计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="66fe6-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="66fe6-147">如果用户满足此要求所定义的最低年龄，则可以认为授权成功。</span><span class="sxs-lookup"><span data-stu-id="66fe6-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="66fe6-148">授权成功后，会调用 `context.Succeed`，使用满足的要求作为其唯一参数。</span><span class="sxs-lookup"><span data-stu-id="66fe6-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="66fe6-149">为多个要求使用处理程序</span><span class="sxs-lookup"><span data-stu-id="66fe6-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="66fe6-150">下面是一个一对多关系的示例, 其中权限处理程序可以处理三种不同类型的要求:</span><span class="sxs-lookup"><span data-stu-id="66fe6-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="66fe6-151">前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;属性, 该属性包含未标记为成功的要求。</span><span class="sxs-lookup"><span data-stu-id="66fe6-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="66fe6-152">`ReadPermission`对于要求, 用户必须是所有者或主办方才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="66fe6-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="66fe6-153">对于`EditPermission` 或`DeletePermission`要求, 该用户必须是访问所请求资源的所有者。</span><span class="sxs-lookup"><span data-stu-id="66fe6-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="66fe6-154">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="66fe6-154">Handler registration</span></span>

<span data-ttu-id="66fe6-155">处理程序是在配置期间在服务集合中注册的。</span><span class="sxs-lookup"><span data-stu-id="66fe6-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="66fe6-156">例如:</span><span class="sxs-lookup"><span data-stu-id="66fe6-156">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="66fe6-157">前面的代码通过`MinimumAgeHandler`调用`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`注册为单一实例。</span><span class="sxs-lookup"><span data-stu-id="66fe6-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="66fe6-158">可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。</span><span class="sxs-lookup"><span data-stu-id="66fe6-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="66fe6-159">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="66fe6-159">What should a handler return?</span></span>

<span data-ttu-id="66fe6-160">请注意，`Handle`处理程序示例[中的 ](#security-authorization-handler-example) 方法不返回值。</span><span class="sxs-lookup"><span data-stu-id="66fe6-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="66fe6-161">如何表明状态是成功还是失败？</span><span class="sxs-lookup"><span data-stu-id="66fe6-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="66fe6-162">处理程序通过调用 `context.Succeed(IAuthorizationRequirement requirement)` 并传递已成功验证的要求来表示成功。</span><span class="sxs-lookup"><span data-stu-id="66fe6-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="66fe6-163">处理程序通常不需要处理失败，因为同一要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="66fe6-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="66fe6-164">若要保证故障, 即使其他要求处理程序成功, `context.Fail`请调用。</span><span class="sxs-lookup"><span data-stu-id="66fe6-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="66fe6-165">如果处理程序调用`context.Succeed`或`context.Fail`, 则仍将调用所有其他处理程序。</span><span class="sxs-lookup"><span data-stu-id="66fe6-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="66fe6-166">这允许要求产生副作用, 如日志记录, 即使另一个处理程序已成功验证或失败, 也会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="66fe6-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="66fe6-167">设置为 `false` 时，[InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 属性 （在 ASP.NET Core 1.1 及更高版本中提供）会在已调用 `context.Fail` 的情况下不执行处理程序。</span><span class="sxs-lookup"><span data-stu-id="66fe6-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="66fe6-168">`InvokeHandlersAfterFailure` 默认为 `true`，这种情况下会调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="66fe6-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="66fe6-169">即使身份验证失败, 也会调用授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="66fe6-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="66fe6-170">为什么需要对一项要求使用多个处理程序？</span><span class="sxs-lookup"><span data-stu-id="66fe6-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="66fe6-171">在需要以 **OR** 逻辑为基础进行评估的情况下，可以针对单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="66fe6-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="66fe6-172">例如，Microsoft 的门只能使用门禁卡打开。</span><span class="sxs-lookup"><span data-stu-id="66fe6-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="66fe6-173">如果你将门禁卡丢在家中，可以要求前台打印一张临时标签来开门。</span><span class="sxs-lookup"><span data-stu-id="66fe6-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="66fe6-174">在这种情况下，只有一个要求，即 *BuildingEntry*，但有多个处理程序，每个处理程序针对单个要求进行检查。</span><span class="sxs-lookup"><span data-stu-id="66fe6-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="66fe6-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="66fe6-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="66fe6-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="66fe6-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="66fe6-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="66fe6-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="66fe6-178">请确保这两个处理程序[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="66fe6-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="66fe6-179">当策略评估 `BuildingEntryRequirement` 时，如果有一个处理程序成功，则策略评估成功。</span><span class="sxs-lookup"><span data-stu-id="66fe6-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="66fe6-180">使用 func 来实现策略</span><span class="sxs-lookup"><span data-stu-id="66fe6-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="66fe6-181">有些情况下，策略很容易用代码实现。</span><span class="sxs-lookup"><span data-stu-id="66fe6-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="66fe6-182">可以在通过 `Func<AuthorizationHandlerContext, bool>` 策略生成器配置策略时提供 `RequireAssertion`。</span><span class="sxs-lookup"><span data-stu-id="66fe6-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="66fe6-183">例如，上一个 `BadgeEntryHandler` 可以重写，如下所示：</span><span class="sxs-lookup"><span data-stu-id="66fe6-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="66fe6-184">访问处理程序中的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="66fe6-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="66fe6-185">在授权处理程序中实现的 `HandleRequirementAsync` 方法有两个参数：`AuthorizationHandlerContext` 以及你正在处理的 `TRequirement`。</span><span class="sxs-lookup"><span data-stu-id="66fe6-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="66fe6-186">MVC 或 Jabbr 之类的框架可以自由地将任何对象添加到 `Resource` 中的 `AuthorizationHandlerContext` 属性，以便传递额外信息。</span><span class="sxs-lookup"><span data-stu-id="66fe6-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="66fe6-187">例如，MVC 在 [ 属性中传递 ](/dotnet/api/?term=AuthorizationFilterContext)AuthorizationFilterContext`Resource` 实例。</span><span class="sxs-lookup"><span data-stu-id="66fe6-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="66fe6-188">可以通过此属性访问 `HttpContext`、`RouteData` 以及 MVC 和 Razor 页面提供的所有其他内容。</span><span class="sxs-lookup"><span data-stu-id="66fe6-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="66fe6-189">对 `Resource` 属性的使用取决于框架。</span><span class="sxs-lookup"><span data-stu-id="66fe6-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="66fe6-190">使用 `Resource` 属性中的信息时，授权策略就会局限于特定的框架。</span><span class="sxs-lookup"><span data-stu-id="66fe6-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="66fe6-191">应`Resource` `InvalidCastException`使用关键字强制转换属性, 然后确认强制转换已成功, 以确保在其他框架上运行时代码不会崩溃: `is`</span><span class="sxs-lookup"><span data-stu-id="66fe6-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
