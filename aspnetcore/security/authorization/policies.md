---
title: ASP.NET Core中基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序，以强制实施在 ASP.NET Core 应用中的授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2017
uid: security/authorization/policies
ms.openlocfilehash: e72f15c28fb7b62c671dd6475cc323cacce42de6
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208316"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="e0dfd-103">ASP.NET Core中基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="e0dfd-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="e0dfd-104">实际上，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)都使用要求、要求处理程序和预配置的策略。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e0dfd-105">这些构建基块支持在代码中使用授权评估表达式。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e0dfd-106">结果就是，授权结构更加丰富，可重复使用，并且可以测试。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e0dfd-107">授权策略包含一个或多个要求。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e0dfd-108">授权策略包含一个或多个要求，并在 `Startup.ConfigureServices` 方法中作为授权服务配置的一部分注册：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="e0dfd-109">在前面的示例中，创建了一个“AtLeast21”策略。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e0dfd-110">它只有一个要求&mdash;，即最低年龄，以参数的形式传递给要求。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="e0dfd-111">将策略应用到 MVC 控制器</span><span class="sxs-lookup"><span data-stu-id="e0dfd-111">Applying policies to MVC controllers</span></span>

<span data-ttu-id="e0dfd-112">如果使用的 Razor 页面，请参阅[将策略应用于 Razor 页面](#applying-policies-to-razor-pages)本文档中。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-112">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e0dfd-113">策略应用于控制器使用`[Authorize]`策略名称的属性。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-113">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e0dfd-114">例如：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-114">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="e0dfd-115">将策略应用于 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="e0dfd-115">Applying policies to Razor Pages</span></span>

<span data-ttu-id="e0dfd-116">通过将策略应用于 Razor 页面使用`[Authorize]`策略名称的属性。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-116">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e0dfd-117">例如：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-117">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e0dfd-118">策略还会应用于 Razor 页面通过使用[授权约定](xref:security/authorization/razor-pages-authorization)。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-118">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e0dfd-119">要求</span><span class="sxs-lookup"><span data-stu-id="e0dfd-119">Requirements</span></span>

<span data-ttu-id="e0dfd-120">授权要求是一个可供策略用来评估当前用户主体的数据参数的集合。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-120">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e0dfd-121">在我们的“AtLeast21”策略中，此要求是单个参数&mdash;最低年龄。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-121">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e0dfd-122">要求可以实现 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-122">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e0dfd-123">参数化的最低年龄要求可以按如下方式实现：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-123">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e0dfd-124">如果授权策略包含多个授权要求，所有要求必须都通过策略评估成功的顺序。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-124">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e0dfd-125">换而言之，在处理多个添加到单个授权策略的授权要求**AND**基础。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-125">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e0dfd-126">一项要求并不需要具有数据或属性。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-126">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e0dfd-127">授权处理程序</span><span class="sxs-lookup"><span data-stu-id="e0dfd-127">Authorization handlers</span></span>

<span data-ttu-id="e0dfd-128">授权处理程序负责评估要求的属性。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-128">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e0dfd-129">授权处理程序会针对提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 来评估要求，确定是否允许访问。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-129">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e0dfd-130">一项要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-130">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e0dfd-131">处理程序可以继承 [AuthorizationHandler\<<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中的 `TRequirement` 是需处理的要求。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-131">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e0dfd-132">另外，一个处理程序也可以通过实现 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 来处理多个类型的要求。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-132">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e0dfd-133">一个要求使用一个处理程序</span><span class="sxs-lookup"><span data-stu-id="e0dfd-133">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e0dfd-134">下面是一对一关系的示例，其中的单个最低年龄要求处理程序使用单个要求：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-134">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e0dfd-135">前面的代码确定当前的用户主体是否有一个由已知的受信任颁发者颁发的出生日期声明。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-135">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e0dfd-136">缺少声明时，无法进行授权，这种情况下会返回已完成的任务。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-136">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e0dfd-137">存在声明时，会计算用户的年龄。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-137">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e0dfd-138">如果用户满足此要求所定义的最低年龄，则可以认为授权成功。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-138">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e0dfd-139">授权成功后，会调用 `context.Succeed`，使用满足的要求作为其唯一参数。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-139">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e0dfd-140">有关多个要求使用一个处理程序</span><span class="sxs-lookup"><span data-stu-id="e0dfd-140">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e0dfd-141">以下是一个对多关系的权限的处理程序可以在其中处理三种不同类型的要求的示例：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-141">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e0dfd-142">前面的代码会遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;不包含要求的属性标记为成功。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-142">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e0dfd-143">有关`ReadPermission`要求，用户必须是所有者或主办方来访问所请求的资源。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-143">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e0dfd-144">情况下`EditPermission`或`DeletePermission`要求、 他或她必须是所有者才能访问请求的资源。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-144">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e0dfd-145">处理程序注册</span><span class="sxs-lookup"><span data-stu-id="e0dfd-145">Handler registration</span></span>

<span data-ttu-id="e0dfd-146">处理程序是在配置期间在服务集合中注册的。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-146">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e0dfd-147">例如：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-147">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="e0dfd-148">前面的代码中注册`MinimumAgeHandler`作为单一实例通过调用`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-148">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e0dfd-149">可以使用任何内置注册处理程序[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-149">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e0dfd-150">处理程序应返回什么？</span><span class="sxs-lookup"><span data-stu-id="e0dfd-150">What should a handler return?</span></span>

<span data-ttu-id="e0dfd-151">请注意，`Handle`处理程序示例[中的 ](#security-authorization-handler-example) 方法不返回值。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-151">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e0dfd-152">如何表明状态是成功还是失败？</span><span class="sxs-lookup"><span data-stu-id="e0dfd-152">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e0dfd-153">处理程序通过调用 `context.Succeed(IAuthorizationRequirement requirement)` 并传递已成功验证的要求来表示成功。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-153">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e0dfd-154">处理程序通常不需要处理失败，因为同一要求的其他处理程序可能会成功。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-154">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e0dfd-155">若要保证失败，即使其他要求处理程序会成功，请调用`context.Fail`。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-155">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e0dfd-156">设置为 `false` 时，[InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 属性 （在 ASP.NET Core 1.1 及更高版本中提供）会在已调用 `context.Fail` 的情况下不执行处理程序。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-156">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e0dfd-157">`InvokeHandlersAfterFailure` 默认为 `true`，这种情况下会调用所有处理程序。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-157">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span> <span data-ttu-id="e0dfd-158">这样要求以产生副作用，例如日志记录，这些始终发生即使`context.Fail`已在另一个处理程序调用。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-158">This allows requirements to produce side effects, such as logging, which always take place even if `context.Fail` has been called in another handler.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e0dfd-159">为什么需要对一项要求使用多个处理程序？</span><span class="sxs-lookup"><span data-stu-id="e0dfd-159">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e0dfd-160">在需要以 **OR** 逻辑为基础进行评估的情况下，可以针对单个要求实现多个处理程序。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-160">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e0dfd-161">例如，Microsoft 的门只能使用门禁卡打开。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-161">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e0dfd-162">如果你将门禁卡丢在家中，可以要求前台打印一张临时标签来开门。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-162">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e0dfd-163">在这种情况下，只有一个要求，即 *BuildingEntry*，但有多个处理程序，每个处理程序针对单个要求进行检查。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-163">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e0dfd-164">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e0dfd-164">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e0dfd-165">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e0dfd-165">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e0dfd-166">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e0dfd-166">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e0dfd-167">请确保这两个处理程序[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-167">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e0dfd-168">当策略评估 `BuildingEntryRequirement` 时，如果有一个处理程序成功，则策略评估成功。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-168">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e0dfd-169">使用 func 满足策略</span><span class="sxs-lookup"><span data-stu-id="e0dfd-169">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e0dfd-170">有些情况下，策略很容易用代码实现。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-170">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e0dfd-171">可以在通过 `Func<AuthorizationHandlerContext, bool>` 策略生成器配置策略时提供 `RequireAssertion`。
</span><span class="sxs-lookup"><span data-stu-id="e0dfd-171">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e0dfd-172">例如，上一个 `BadgeEntryHandler` 可以重写，如下所示：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-172">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e0dfd-173">访问处理程序中的 MVC 请求上下文</span><span class="sxs-lookup"><span data-stu-id="e0dfd-173">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="e0dfd-174">在授权处理程序中实现的 `HandleRequirementAsync` 方法有两个参数：`AuthorizationHandlerContext` 以及你正在处理的 `TRequirement`。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-174">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e0dfd-175">MVC 或 Jabbr 之类的框架可以自由地将任何对象添加到 `Resource` 中的 `AuthorizationHandlerContext` 属性，以便传递额外信息。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-175">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e0dfd-176">例如，MVC 在 [ 属性中传递 ](/dotnet/api/?term=AuthorizationFilterContext)AuthorizationFilterContext`Resource` 实例。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-176">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="e0dfd-177">可以通过此属性访问 `HttpContext`、`RouteData` 以及 MVC 和 Razor 页面提供的所有其他内容。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-177">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e0dfd-178">对 `Resource` 属性的使用取决于框架。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-178">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e0dfd-179">使用 `Resource` 属性中的信息时，授权策略就会局限于特定的框架。</span><span class="sxs-lookup"><span data-stu-id="e0dfd-179">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e0dfd-180">应强制转换`Resource`属性使用`is`关键字，然后确认已成功转换，以确保你的代码不会崩溃与`InvalidCastException`其他框架上运行时：</span><span class="sxs-lookup"><span data-stu-id="e0dfd-180">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
