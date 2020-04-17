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
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET核心中的基于策略的授权

::: moniker range=">= aspnetcore-3.0"

在封面下方，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)使用要求、需求处理程序和预配置的策略。 这些构建基块支持代码中授权评估的表达式。 结果是更丰富、可重用、可测试的授权结构。

授权策略由一个或多个要求组成。 它在 方法中注册为授权服务配置的一`Startup.ConfigureServices`部分：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

在前面的示例中，将创建一个"AtLeast21"策略。 它有一个最低年龄&mdash;的单一要求，作为要求的参数提供。

## <a name="iauthorizationservice"></a>I 授权服务 

确定授权是否成功的主要服务是<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>：

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

前面的代码突出显示了[I 授权服务的](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)两种方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是一个没有方法的标记服务，是跟踪授权是否成功的机制。

每个<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>公司负责检查是否满足要求：
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

类<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>是处理程序用于标记是否满足要求的内容：

```csharp
 context.Succeed(requirement)
```

以下代码显示授权服务的简化（并带有注释）默认实现：

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

以下代码显示了典型的`ConfigureServices`：

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

使用<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>或`[Authorize(Policy = "Something")]`授权。

## <a name="applying-policies-to-mvc-controllers"></a>将策略应用于 MVC 控制器

如果您使用的是 Razor 页面，请参阅本文档中[将策略应用于 Razor 页面](#applying-policies-to-razor-pages)。

策略通过使用具有策略名称`[Authorize]`的属性应用于控制器。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>将策略应用于剃刀页面

策略通过使用具有策略名称`[Authorize]`的属性应用于 Razor 页面。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

策略也可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization)应用于 Razor 页面。

## <a name="requirements"></a>要求

授权要求是策略可用于评估当前用户主体的数据参数的集合。 在我们的"AtLeast21"政策中，要求是最小年龄的单个&mdash;参数。 要求实现[I 授权要求](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。 参数化的最低年龄要求可执行如下：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授权策略包含多个授权要求，则必须通过所有要求才能成功策略评估。 换句话说，添加到单个授权策略中的多个授权要求将基于**AND**处理。

> [!NOTE]
> 要求不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责评估需求的属性。 授权处理程序根据提供的[授权处理程序计算](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)需求，以确定是否允许访问。

要求可以[有多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序可以继承[授权处理程序\<t要求>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中`TRequirement`需要处理。 或者，处理程序可以实现[I授权处理程序](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。

### <a name="use-a-handler-for-one-requirement"></a>对一个要求使用处理程序

<a name="security-authorization-handler-example"></a>

下面是一对一关系的示例，其中最小年龄处理程序使用单个要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前面的代码确定当前用户主体是否具有由已知和受信任的颁发者颁发的出生日期声明。 当缺少声明时，无法发生授权，在这种情况下，将返回已完成的任务。 当声明存在时，将计算用户的年龄。 如果用户满足要求定义的最低年龄，则授权被视为成功。 当授权成功时，`context.Succeed`将调用满足的要求作为其唯一参数。

### <a name="use-a-handler-for-multiple-requirements"></a>对多个要求使用处理程序

下面是一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

前面的代码遍历[Pending要求](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;包含未标记为成功的要求的属性。 对于要求`ReadPermission`，用户必须是所有者或发起人才能访问请求的资源。 如果是`EditPermission`或`DeletePermission`要求，他或她必须是访问请求的资源的所有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

在配置期间，处理程序在服务集合中注册。 例如：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

前面的代码通过调用`MinimumAgeHandler``services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`注册为单例。 处理程序可以使用任何内置[服务生存期进行](xref:fundamentals/dependency-injection#service-lifetimes)注册。

## <a name="what-should-a-handler-return"></a>处理程序应返回什么？

请注意，`Handle`[处理程序示例中](#security-authorization-handler-example)的方法不返回任何值。 如何指示成功或失败的状态？

* 处理程序通过调用`context.Succeed(IAuthorizationRequirement requirement)`来表示成功，传递已成功验证的要求。

* 处理程序通常不需要处理故障，因为相同要求的其他处理程序可能会成功。

* 为了保证失败，即使其他需求处理程序成功，请调用`context.Fail`。

如果处理程序调用`context.Succeed`或`context.Fail`，仍调用所有其他处理程序。 这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或未通过要求也是如此。 当设置为`false`[时，InvokeHandlersAfter 失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（在 ASP.NET Core 1.1 和更高版本中可用）在调用时`context.Fail`短路处理程序的执行。 `InvokeHandlersAfterFailure`默认值为`true`，在这种情况下，将调用所有处理程序。

> [!NOTE]
> 即使身份验证失败，也会调用授权处理程序。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么需要多个处理程序来满足需求？

如果希望评估基于**OR，** 则为单个要求实现多个处理程序。 例如，微软的大门只打开钥匙卡。 如果您将钥匙卡留在家中，接待员会打印临时贴纸，并为您开门。 在这种情况下，您将有一个要求，*即"构建入口*"，但有多个处理程序，每个处理程序都检查单个要求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 如果任一处理程序在策略计算 时成功`BuildingEntryRequirement`，则策略计算将成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 实现策略

在某些情况下，实现策略在代码中很容易表达。 在与策略生成器配置策略时，`Func<AuthorizationHandlerContext, bool>``RequireAssertion`可以提供 .

例如，可以重写前`BadgeEntryHandler`一个，如下所示：

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>在处理程序中访问 MVC 请求上下文

在`HandleRequirementAsync`授权处理程序中实现的方法有两个参数：和`AuthorizationHandlerContext``TRequirement`正在处理的参数。 MVC 或 Jabbr 等框架可免费向`Resource`的属性添加任何对象`AuthorizationHandlerContext`以传递额外信息。

例如，MVC 在属性中传递[授权筛选器上下文](/dotnet/api/?term=AuthorizationFilterContext)的`Resource`实例。 此属性提供对`HttpContext`的`RouteData`访问权限，以及 MVC 和 Razor 页面提供的所有内容。

`Resource`属性的使用是特定于框架的。 使用 属性中`Resource`的信息会将授权策略限制为特定框架。 应使用`Resource``is`关键字强制转换属性，然后确认强制转换已成功确保代码在其他框架上运行时不会崩溃与 。 `InvalidCastException`

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

在封面下方，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)使用要求、需求处理程序和预配置的策略。 这些构建基块支持代码中授权评估的表达式。 结果是更丰富、可重用、可测试的授权结构。

授权策略由一个或多个要求组成。 它在 方法中注册为授权服务配置的一`Startup.ConfigureServices`部分：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

在前面的示例中，将创建一个"AtLeast21"策略。 它有一个最低年龄&mdash;的单一要求，作为要求的参数提供。

## <a name="iauthorizationservice"></a>I 授权服务 

确定授权是否成功的主要服务是<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>：

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

前面的代码突出显示了[I 授权服务的](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)两种方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是一个没有方法的标记服务，是跟踪授权是否成功的机制。

每个<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>公司负责检查是否满足要求：
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

类<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>是处理程序用于标记是否满足要求的内容：

```csharp
 context.Succeed(requirement)
```

以下代码显示授权服务的简化（并带有注释）默认实现：

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

以下代码显示了典型的`ConfigureServices`：

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

使用<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>或`[Authorize(Policy = "Something")]`授权。

## <a name="applying-policies-to-mvc-controllers"></a>将策略应用于 MVC 控制器

如果您使用的是 Razor 页面，请参阅本文档中[将策略应用于 Razor 页面](#applying-policies-to-razor-pages)。

策略通过使用具有策略名称`[Authorize]`的属性应用于控制器。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>将策略应用于剃刀页面

策略通过使用具有策略名称`[Authorize]`的属性应用于 Razor 页面。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

策略也可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization)应用于 Razor 页面。

## <a name="requirements"></a>要求

授权要求是策略可用于评估当前用户主体的数据参数的集合。 在我们的"AtLeast21"政策中，要求是最小年龄的单个&mdash;参数。 要求实现[I 授权要求](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。 参数化的最低年龄要求可执行如下：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授权策略包含多个授权要求，则必须通过所有要求才能成功策略评估。 换句话说，添加到单个授权策略中的多个授权要求将基于**AND**处理。

> [!NOTE]
> 要求不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责评估需求的属性。 授权处理程序根据提供的[授权处理程序计算](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)需求，以确定是否允许访问。

要求可以[有多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序可以继承[授权处理程序\<t要求>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中`TRequirement`需要处理。 或者，处理程序可以实现[I授权处理程序](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler)来处理多种类型的要求。

### <a name="use-a-handler-for-one-requirement"></a>对一个要求使用处理程序

<a name="security-authorization-handler-example"></a>

下面是一对一关系的示例，其中最小年龄处理程序使用单个要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前面的代码确定当前用户主体是否具有由已知和受信任的颁发者颁发的出生日期声明。 当缺少声明时，无法发生授权，在这种情况下，将返回已完成的任务。 当声明存在时，将计算用户的年龄。 如果用户满足要求定义的最低年龄，则授权被视为成功。 当授权成功时，`context.Succeed`将调用满足的要求作为其唯一参数。

### <a name="use-a-handler-for-multiple-requirements"></a>对多个要求使用处理程序

下面是一对多关系的示例，其中权限处理程序可以处理三种不同类型的要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

前面的代码遍历[Pending要求](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;包含未标记为成功的要求的属性。 对于要求`ReadPermission`，用户必须是所有者或发起人才能访问请求的资源。 如果是`EditPermission`或`DeletePermission`要求，他或她必须是访问请求的资源的所有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

在配置期间，处理程序在服务集合中注册。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

前面的代码通过调用`MinimumAgeHandler``services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`注册为单例。 处理程序可以使用任何内置[服务生存期进行](xref:fundamentals/dependency-injection#service-lifetimes)注册。

## <a name="what-should-a-handler-return"></a>处理程序应返回什么？

请注意，`Handle`[处理程序示例中](#security-authorization-handler-example)的方法不返回任何值。 如何指示成功或失败的状态？

* 处理程序通过调用`context.Succeed(IAuthorizationRequirement requirement)`来表示成功，传递已成功验证的要求。

* 处理程序通常不需要处理故障，因为相同要求的其他处理程序可能会成功。

* 为了保证失败，即使其他需求处理程序成功，请调用`context.Fail`。

如果处理程序调用`context.Succeed`或`context.Fail`，仍调用所有其他处理程序。 这允许要求产生副作用，如日志记录，即使另一个处理程序已成功验证或未通过要求也是如此。 当设置为`false`[时，InvokeHandlersAfter 失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure)属性（在 ASP.NET Core 1.1 和更高版本中可用）在调用时`context.Fail`短路处理程序的执行。 `InvokeHandlersAfterFailure`默认值为`true`，在这种情况下，将调用所有处理程序。

> [!NOTE]
> 即使身份验证失败，也会调用授权处理程序。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么需要多个处理程序来满足需求？

如果希望评估基于**OR，** 则为单个要求实现多个处理程序。 例如，微软的大门只打开钥匙卡。 如果您将钥匙卡留在家中，接待员会打印临时贴纸，并为您开门。 在这种情况下，您将有一个要求，*即"构建入口*"，但有多个处理程序，每个处理程序都检查单个要求。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

确保两个处理程序都[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 如果任一处理程序在策略计算 时成功`BuildingEntryRequirement`，则策略计算将成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 实现策略

在某些情况下，实现策略在代码中很容易表达。 在与策略生成器配置策略时，`Func<AuthorizationHandlerContext, bool>``RequireAssertion`可以提供 .

例如，可以重写前`BadgeEntryHandler`一个，如下所示：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>在处理程序中访问 MVC 请求上下文

在`HandleRequirementAsync`授权处理程序中实现的方法有两个参数：和`AuthorizationHandlerContext``TRequirement`正在处理的参数。 MVC 或 SignalR 等框架可免费向`Resource`的属性添加任何对象`AuthorizationHandlerContext`以传递额外信息。

使用终结点路由时，授权通常由授权中间件处理。 在这种情况下，`Resource`属性是 的<xref:Microsoft.AspNetCore.Http.Endpoint>实例。 终结点可用于探测要路由到的资源的基础。 例如：

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

对于传统路由，或者当授权作为 MVC 授权筛选器的一部分发生时，值`Resource`是实例。 <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 此属性提供对`HttpContext`的`RouteData`访问权限，以及 MVC 和 Razor 页面提供的所有内容。

`Resource`属性的使用是特定于框架的。 使用 属性中`Resource`的信息会将授权策略限制为特定框架。 应使用`Resource``is`关键字强制转换属性，然后确认强制转换已成功确保代码在其他框架上运行时不会崩溃与 。 `InvalidCastException`

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
