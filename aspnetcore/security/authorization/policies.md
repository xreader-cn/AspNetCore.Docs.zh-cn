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
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core中基于策略的授权

实际上，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)都使用要求、要求处理程序和预配置的策略。 这些构建基块支持在代码中使用授权评估表达式。 结果就是，授权结构更加丰富，可重复使用，并且可以测试。

授权策略包含一个或多个要求。 授权策略包含一个或多个要求，并在 `Startup.ConfigureServices` 方法中作为授权服务配置的一部分注册：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

在前面的示例中，创建了一个“AtLeast21”策略。 它只有一个要求&mdash;，即最低年龄，以参数的形式传递给要求。

## <a name="iauthorizationservice"></a>IAuthorizationService 

确定授权是否成功的主要服务是<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

前面的代码突出显示了[IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)的两种方法。

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>是无方法的标记服务, 以及用于跟踪授权是否成功的机制。

每<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>个负责检查是否满足要求:
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

此<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>类是处理程序用来标记是否已满足要求的类:

```csharp
 context.Succeed(requirement)
```

下面的代码显示授权服务的默认实现 (和批注批注) 的默认实现:

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

下面的代码演示了一个`ConfigureServices`典型的:

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

使用<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 或`[Authorize(Policy = "Something")]`进行授权。

## <a name="applying-policies-to-mvc-controllers"></a>将策略应用到 MVC 控制器

如果使用 Razor Pages, 请参阅本文档中的[将策略应用于 Razor Pages](#applying-policies-to-razor-pages) 。

策略通过使用`[Authorize]`具有策略名称的属性应用到控制器。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>将策略应用到 Razor Pages

通过将`[Authorize]`属性与策略名称一起使用, 将策略应用到 Razor Pages。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

还可以通过使用[授权约定](xref:security/authorization/razor-pages-authorization), 将策略应用到 Razor Pages。

## <a name="requirements"></a>要求

授权要求是一个可供策略用来评估当前用户主体的数据参数的集合。 在我们的“AtLeast21”策略中，此要求是单个参数&mdash;最低年龄。 要求可以实现 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。 参数化的最低年龄要求可以按如下方式实现：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授权策略包含多个授权要求, 则所有要求必须通过, 才能成功进行策略评估。 换句话说, 添加到单个授权策略中的多个授权要求将分别处理。

> [!NOTE]
> 要求不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责评估要求的属性。 授权处理程序会针对提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 来评估要求，确定是否允许访问。

一项要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序可以继承 [AuthorizationHandler\<<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中的 `TRequirement` 是需处理的要求。 另外，一个处理程序也可以通过实现 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 来处理多个类型的要求。

### <a name="use-a-handler-for-one-requirement"></a>为一个要求使用处理程序

<a name="security-authorization-handler-example"></a>

下面是一对一关系的示例，其中的单个最低年龄要求处理程序使用单个要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前面的代码确定当前的用户主体是否有一个由已知的受信任颁发者颁发的出生日期声明。 缺少声明时，无法进行授权，这种情况下会返回已完成的任务。 存在声明时，会计算用户的年龄。 如果用户满足此要求所定义的最低年龄，则可以认为授权成功。 授权成功后，会调用 `context.Succeed`，使用满足的要求作为其唯一参数。

### <a name="use-a-handler-for-multiple-requirements"></a>为多个要求使用处理程序

下面是一个一对多关系的示例, 其中权限处理程序可以处理三种不同类型的要求:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

前面的代码遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;属性, 该属性包含未标记为成功的要求。 `ReadPermission`对于要求, 用户必须是所有者或主办方才能访问请求的资源。 对于`EditPermission` 或`DeletePermission`要求, 该用户必须是访问所请求资源的所有者。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

处理程序是在配置期间在服务集合中注册的。 例如:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

前面的代码通过`MinimumAgeHandler`调用`services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`注册为单一实例。 可以使用任何内置[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)来注册处理程序。

## <a name="what-should-a-handler-return"></a>处理程序应返回什么？

请注意，`Handle`处理程序示例[中的 ](#security-authorization-handler-example) 方法不返回值。 如何表明状态是成功还是失败？

* 处理程序通过调用 `context.Succeed(IAuthorizationRequirement requirement)` 并传递已成功验证的要求来表示成功。

* 处理程序通常不需要处理失败，因为同一要求的其他处理程序可能会成功。

* 若要保证故障, 即使其他要求处理程序成功, `context.Fail`请调用。

如果处理程序调用`context.Succeed`或`context.Fail`, 则仍将调用所有其他处理程序。 这允许要求产生副作用, 如日志记录, 即使另一个处理程序已成功验证或失败, 也会发生这种情况。 设置为 `false` 时，[InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 属性 （在 ASP.NET Core 1.1 及更高版本中提供）会在已调用 `context.Fail` 的情况下不执行处理程序。 `InvokeHandlersAfterFailure` 默认为 `true`，这种情况下会调用所有处理程序。

> [!NOTE]
> 即使身份验证失败, 也会调用授权处理程序。

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>为什么需要对一项要求使用多个处理程序？

在需要以 **OR** 逻辑为基础进行评估的情况下，可以针对单个要求实现多个处理程序。 例如，Microsoft 的门只能使用门禁卡打开。 如果你将门禁卡丢在家中，可以要求前台打印一张临时标签来开门。 在这种情况下，只有一个要求，即 *BuildingEntry*，但有多个处理程序，每个处理程序针对单个要求进行检查。

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

请确保这两个处理程序[已注册](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)。 当策略评估 `BuildingEntryRequirement` 时，如果有一个处理程序成功，则策略评估成功。

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 来实现策略

有些情况下，策略很容易用代码实现。 可以在通过 `Func<AuthorizationHandlerContext, bool>` 策略生成器配置策略时提供 `RequireAssertion`。

例如，上一个 `BadgeEntryHandler` 可以重写，如下所示：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>访问处理程序中的 MVC 请求上下文

在授权处理程序中实现的 `HandleRequirementAsync` 方法有两个参数：`AuthorizationHandlerContext` 以及你正在处理的 `TRequirement`。 MVC 或 Jabbr 之类的框架可以自由地将任何对象添加到 `Resource` 中的 `AuthorizationHandlerContext` 属性，以便传递额外信息。

例如，MVC 在 [ 属性中传递 ](/dotnet/api/?term=AuthorizationFilterContext)AuthorizationFilterContext`Resource` 实例。 可以通过此属性访问 `HttpContext`、`RouteData` 以及 MVC 和 Razor 页面提供的所有其他内容。

对 `Resource` 属性的使用取决于框架。 使用 `Resource` 属性中的信息时，授权策略就会局限于特定的框架。 应`Resource` `InvalidCastException`使用关键字强制转换属性, 然后确认强制转换已成功, 以确保在其他框架上运行时代码不会崩溃: `is`

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
