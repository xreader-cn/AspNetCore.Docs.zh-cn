---
title: ASP.NET Core 中基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序，以在 ASP.NET Core 的应用程序中强制实施授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: e3929fb0f45d4ba28f46a6b42b653940de0badb0
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761034"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core 中基于策略的授权

::: moniker range=">= aspnetcore-3.0"

[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)，都使用了要求、要求处理程序和预配置的策略。 这些构建基块支持代码中授权评估的表达式。 结果是一种更丰富的可重复使用的授权结构。

授权策略包括一个或多个要求。 在 `Startup.ConfigureServices` 方法中，将其注册为授权服务配置的一部分：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

在前面的示例中，创建了 "AtLeast21" 策略。 它只有最短期限&mdash;的一种要求，它作为要求的参数提供。

## <a name="iauthorizationservice"></a>IAuthorizationService 

确定授权是否成功的主要服务是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>：

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

前面的代码突出显示了[IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 是一种不带方法的标记服务，以及用于跟踪授权是否成功的机制。

每个 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 负责检查是否满足要求：
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

处理程序使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 类来标记是否已满足要求：

```csharp
 context.Succeed(requirement)
```

下面的代码显示授权服务的默认实现（和批注批注）的默认实现：

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

下面的代码演示典型 `ConfigureServices`：

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

使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 进行授权。

## <a name="applying-policies-to-mvc-controllers"></a>将策略应用到 MVC 控制器

如果使用 Razor Pages，请参阅本文档中的[将策略应用于 Razor Pages](#applying-policies-to-razor-pages) 。

策略通过使用具有策略名称的 `[Authorize]` 属性应用到控制器。 例如:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>将策略应用到 Razor Pages

通过将 `[Authorize]` 属性与策略名称一起使用，将策略应用到 Razor Pages。 例如:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

还可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization)，将策略应用到 Razor Pages。

## <a name="requirements"></a>要求

授权要求是一个数据参数集合，策略可以使用这些参数来评估当前用户主体。 在我们的 "AtLeast21" 策略中，要求是单个参数&mdash;最小年龄。 要求实现[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，它是一个空的标记接口。 可以按如下所示实现参数化最低期限要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授权策略包含多个授权要求，则所有要求必须通过，才能成功进行策略评估。 换句话说，添加到单个授权策略中的多个授权要求**将分别处理**。

> [!NOTE]
> 要求不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责计算要求的属性。 授权处理程序根据提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)评估要求，以确定是否允许访问。

要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序可能会继承[AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是需要处理的。 或者，处理程序可以实现[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。

### <a name="use-a-handler-for-one-requirement"></a>为一个要求使用处理程序

<a name="security-authorization-handler-example"></a>

下面是一种一对一关系的示例，其中最小 age 处理程序利用了单一要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

上述代码确定当前用户主体是否具有由已知的可信颁发者颁发的出生日期。 当缺少声明时无法进行授权，在这种情况下，将返回已完成的任务。 当声明存在时，将计算用户的年龄。 如果用户达到了要求所定义的最小时间，则会认为授权成功。 授权成功后，会调用 `context.Succeed`，并将满足要求作为其唯一参数。

### <a name="use-a-handler-for-multiple-requirements"></a>为多个要求使用处理程序

下面是一个一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;包含未标记为成功的要求的属性。 对于 `ReadPermission` 要求，用户必须是所有者或主办方才能访问请求的资源。 如果是 `EditPermission` 或 `DeletePermission` 要求，则该用户必须是访问所请求资源的所有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

在配置过程中，将在服务集合中注册处理程序。 例如:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

前面的代码通过调用 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`将 `MinimumAgeHandler` 注册为单一实例。 可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。

## <a name="what-should-a-handler-return"></a>处理程序应返回什么？

请注意，[处理程序示例](#security-authorization-handler-example)中的 `Handle` 方法不返回值。 表示成功或失败的状态如何？

* 处理程序通过调用 `context.Succeed(IAuthorizationRequirement requirement)`来指示成功，同时传递已成功验证的要求。

* 处理程序通常不需要处理故障，因为同一要求的其他处理程序可能会成功。

* 为了保证故障，即使其他要求处理程序成功，也 `context.Fail`。

如果处理程序调用 `context.Succeed` 或 `context.Fail`，则仍将调用所有其他处理程序。 这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或失败，也会发生这种情况。 如果设置为 "`false`"，则在调用 `context.Fail` 时， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（在1.1 和更高版本 ASP.NET Core 中提供）会将处理程序的执行操作短路。 `InvokeHandlersAfterFailure` 默认为 `true`，在这种情况下，将调用所有处理程序。

> [!NOTE]
> 即使身份验证失败，也会调用授权处理程序。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么需要多个处理程序才能实现要求？

如果希望计算基于**或**，请为单个要求实现多个处理程序。 例如，Microsoft 的门只有用密钥卡打开。 如果在家中保留了密钥卡，则接待员会打印一个临时贴纸，并为您打开门。 在这种情况下，你将有一个要求*BuildingEntry*，但有多个处理程序，每个处理程序都检查单个需求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 如果某个处理程序在某一策略评估 `BuildingEntryRequirement`时成功，则策略评估将成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 来实现策略

在某些情况下，实现策略的情况非常简单，只是在代码中表达。 使用 `RequireAssertion` 策略生成器配置策略时，可以提供 `Func<AuthorizationHandlerContext, bool>`。

例如，可以按如下所示重写上一 `BadgeEntryHandler`：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>在处理程序中访问 MVC 请求上下文

在授权处理程序中实现的 `HandleRequirementAsync` 方法具有两个参数： `AuthorizationHandlerContext` 和正在处理的 `TRequirement`。 MVC 或 Jabbr 等框架可自由地将任何对象添加到 `AuthorizationHandlerContext` 上的 `Resource` 属性，以传递额外的信息。

例如，MVC 在 `Resource` 属性中传递[AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)的实例。 此属性提供对 `HttpContext`、`RouteData`以及 MVC 和 Razor Pages 提供的所有其他内容的访问权限。

使用 `Resource` 属性是特定于框架的。 使用 `Resource` 属性中的信息可将授权策略限制为特定框架。 应使用 `is` 关键字强制转换 `Resource` 属性，然后确认强制转换已成功，以确保在其他框架上运行时代码不会崩溃 `InvalidCastException`：

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

[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)，都使用了要求、要求处理程序和预配置的策略。 这些构建基块支持代码中授权评估的表达式。 结果是一种更丰富的可重复使用的授权结构。

授权策略包括一个或多个要求。 在 `Startup.ConfigureServices` 方法中，将其注册为授权服务配置的一部分：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

在前面的示例中，创建了 "AtLeast21" 策略。 它只有最短期限&mdash;的一种要求，它作为要求的参数提供。

## <a name="iauthorizationservice"></a>IAuthorizationService 

确定授权是否成功的主要服务是 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>：

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

前面的代码突出显示了[IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 是一种不带方法的标记服务，以及用于跟踪授权是否成功的机制。

每个 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 负责检查是否满足要求：
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

处理程序使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 类来标记是否已满足要求：

```csharp
 context.Succeed(requirement)
```

下面的代码显示授权服务的默认实现（和批注批注）的默认实现：

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

下面的代码演示典型 `ConfigureServices`：

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

使用 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或 `[Authorize(Policy = "Something")]` 进行授权。

## <a name="applying-policies-to-mvc-controllers"></a>将策略应用到 MVC 控制器

如果使用 Razor Pages，请参阅本文档中的[将策略应用于 Razor Pages](#applying-policies-to-razor-pages) 。

策略通过使用具有策略名称的 `[Authorize]` 属性应用到控制器。 例如:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>将策略应用到 Razor Pages

通过将 `[Authorize]` 属性与策略名称一起使用，将策略应用到 Razor Pages。 例如:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

还可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization)，将策略应用到 Razor Pages。

## <a name="requirements"></a>要求

授权要求是一个数据参数集合，策略可以使用这些参数来评估当前用户主体。 在我们的 "AtLeast21" 策略中，要求是单个参数&mdash;最小年龄。 要求实现[IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，它是一个空的标记接口。 可以按如下所示实现参数化最低期限要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授权策略包含多个授权要求，则所有要求必须通过，才能成功进行策略评估。 换句话说，添加到单个授权策略中的多个授权要求**将分别处理**。

> [!NOTE]
> 要求不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责计算要求的属性。 授权处理程序根据提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)评估要求，以确定是否允许访问。

要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序可能会继承[AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中 `TRequirement` 是需要处理的。 或者，处理程序可以实现[IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。

### <a name="use-a-handler-for-one-requirement"></a>为一个要求使用处理程序

<a name="security-authorization-handler-example"></a>

下面是一种一对一关系的示例，其中最小 age 处理程序利用了单一要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

上述代码确定当前用户主体是否具有由已知的可信颁发者颁发的出生日期。 当缺少声明时无法进行授权，在这种情况下，将返回已完成的任务。 当声明存在时，将计算用户的年龄。 如果用户达到了要求所定义的最小时间，则会认为授权成功。 授权成功后，会调用 `context.Succeed`，并将满足要求作为其唯一参数。

### <a name="use-a-handler-for-multiple-requirements"></a>为多个要求使用处理程序

下面是一个一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;包含未标记为成功的要求的属性。 对于 `ReadPermission` 要求，用户必须是所有者或主办方才能访问请求的资源。 如果是 `EditPermission` 或 `DeletePermission` 要求，则该用户必须是访问所请求资源的所有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

在配置过程中，将在服务集合中注册处理程序。 例如:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

前面的代码通过调用 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`将 `MinimumAgeHandler` 注册为单一实例。 可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。

## <a name="what-should-a-handler-return"></a>处理程序应返回什么？

请注意，[处理程序示例](#security-authorization-handler-example)中的 `Handle` 方法不返回值。 表示成功或失败的状态如何？

* 处理程序通过调用 `context.Succeed(IAuthorizationRequirement requirement)`来指示成功，同时传递已成功验证的要求。

* 处理程序通常不需要处理故障，因为同一要求的其他处理程序可能会成功。

* 为了保证故障，即使其他要求处理程序成功，也 `context.Fail`。

如果处理程序调用 `context.Succeed` 或 `context.Fail`，则仍将调用所有其他处理程序。 这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或失败，也会发生这种情况。 如果设置为 "`false`"，则在调用 `context.Fail` 时， [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（在1.1 和更高版本 ASP.NET Core 中提供）会将处理程序的执行操作短路。 `InvokeHandlersAfterFailure` 默认为 `true`，在这种情况下，将调用所有处理程序。

> [!NOTE]
> 即使身份验证失败，也会调用授权处理程序。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么需要多个处理程序才能实现要求？

如果希望计算基于**或**，请为单个要求实现多个处理程序。 例如，Microsoft 的门只有用密钥卡打开。 如果在家中保留了密钥卡，则接待员会打印一个临时贴纸，并为您打开门。 在这种情况下，你将有一个要求*BuildingEntry*，但有多个处理程序，每个处理程序都检查单个需求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 如果某个处理程序在某一策略评估 `BuildingEntryRequirement`时成功，则策略评估将成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 来实现策略

在某些情况下，实现策略的情况非常简单，只是在代码中表达。 使用 `RequireAssertion` 策略生成器配置策略时，可以提供 `Func<AuthorizationHandlerContext, bool>`。

例如，可以按如下所示重写上一 `BadgeEntryHandler`：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>在处理程序中访问 MVC 请求上下文

在授权处理程序中实现的 `HandleRequirementAsync` 方法具有两个参数： `AuthorizationHandlerContext` 和正在处理的 `TRequirement`。 MVC 或 Jabbr 等框架可自由地将任何对象添加到 `AuthorizationHandlerContext` 上的 `Resource` 属性，以传递额外的信息。

例如，MVC 在 `Resource` 属性中传递[AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)的实例。 此属性提供对 `HttpContext`、`RouteData`以及 MVC 和 Razor Pages 提供的所有其他内容的访问权限。

使用 `Resource` 属性是特定于框架的。 使用 `Resource` 属性中的信息可将授权策略限制为特定框架。 应使用 `is` 关键字强制转换 `Resource` 属性，然后确认强制转换已成功，以确保在其他框架上运行时代码不会崩溃 `InvalidCastException`：

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end