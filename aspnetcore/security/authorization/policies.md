---
title: ASP.NET Core中基于策略的授权
author: rick-anderson
description: 了解如何创建和使用授权策略处理程序，以强制实施在 ASP.NET Core 应用中的授权要求。
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2017
uid: security/authorization/policies
ms.openlocfilehash: 937c73c26cd3935c5069d4735e754d1a567f41f4
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248103"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core中基于策略的授权

实际上，[基于角色的授权](xref:security/authorization/roles)和[基于声明的授权](xref:security/authorization/claims)都使用要求、要求处理程序和预配置的策略。 这些构建基块支持在代码中使用授权评估表达式。 结果就是，授权结构更加丰富，可重复使用，并且可以测试。

授权策略包含一个或多个要求。 授权策略包含一个或多个要求，并在 `Startup.ConfigureServices` 方法中作为授权服务配置的一部分注册：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

在前面的示例中，创建了一个“AtLeast21”策略。 它只有一个要求&mdash;，即最低年龄，以参数的形式传递给要求。

将 `[Authorize]` 属性和策略名称配合使用即可应用策略。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a>要求

授权要求是一个可供策略用来评估当前用户主体的数据参数的集合。 在我们的“AtLeast21”策略中，此要求是单个参数&mdash;最低年龄。 要求可以实现 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)，这是一个空标记接口。 参数化的最低年龄要求可以按如下方式实现：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

如果授权策略包含多个授权要求，所有要求必须都通过策略评估成功的顺序。 换而言之，在处理多个添加到单个授权策略的授权要求**AND**基础。

> [!NOTE]
> 一项要求并不需要具有数据或属性。

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>授权处理程序

授权处理程序负责评估要求的属性。 授权处理程序会针对提供的[AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 来评估要求，确定是否允许访问。

一项要求可以有[多个处理程序](#security-authorization-policies-based-multiple-handlers)。 处理程序可以继承 [AuthorizationHandler\<<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)，其中的 `TRequirement` 是需处理的要求。 另外，一个处理程序也可以通过实现 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 来处理多个类型的要求。

### <a name="use-a-handler-for-one-requirement"></a>一个要求使用一个处理程序

<a name="security-authorization-handler-example"></a>

下面是一对一关系的示例，其中的单个最低年龄要求处理程序使用单个要求：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

前面的代码确定当前的用户主体是否有一个由已知的受信任颁发者颁发的出生日期声明。 缺少声明时，无法进行授权，这种情况下会返回已完成的任务。 存在声明时，会计算用户的年龄。 如果用户满足此要求所定义的最低年龄，则可以认为授权成功。 授权成功后，会调用 `context.Succeed`，使用满足的要求作为其唯一参数。

### <a name="use-a-handler-for-multiple-requirements"></a>有关多个要求使用一个处理程序

以下是一个对多关系的权限的处理程序可以在其中处理三种不同类型的要求的示例：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

前面的代码会遍历[PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;不包含要求的属性标记为成功。 有关`ReadPermission`要求，用户必须是所有者或主办方来访问所请求的资源。 情况下`EditPermission`或`DeletePermission`要求、 他或她必须是所有者才能访问请求的资源。

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>处理程序注册

处理程序是在配置期间在服务集合中注册的。 例如：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

每个处理程序均通过调用 `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();` 添加到服务集合。

## <a name="what-should-a-handler-return"></a>处理程序应返回什么？

请注意，`Handle`处理程序示例[中的 ](#security-authorization-handler-example) 方法不返回值。 如何表明状态是成功还是失败？

* 处理程序通过调用 `context.Succeed(IAuthorizationRequirement requirement)` 并传递已成功验证的要求来表示成功。

* 处理程序通常不需要处理失败，因为同一要求的其他处理程序可能会成功。

* 若要保证失败，即使其他要求处理程序会成功，请调用`context.Fail`。

设置为 `false` 时，[InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 属性 （在 ASP.NET Core 1.1 及更高版本中提供）会在已调用 `context.Fail` 的情况下不执行处理程序。 `InvokeHandlersAfterFailure` 默认为 `true`，这种情况下会调用所有处理程序。 这样要求以产生副作用，例如日志记录，这些始终发生即使`context.Fail`已在另一个处理程序调用。

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

## <a name="using-a-func-to-fulfill-a-policy"></a>使用 func 满足策略

有些情况下，策略很容易用代码实现。 可以在通过 `Func<AuthorizationHandlerContext, bool>` 策略生成器配置策略时提供 `RequireAssertion`。


例如，上一个 `BadgeEntryHandler` 可以重写，如下所示：

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a>访问处理程序中的 MVC 请求上下文

在授权处理程序中实现的 `HandleRequirementAsync` 方法有两个参数：`AuthorizationHandlerContext` 以及你正在处理的 `TRequirement`。 MVC 或 Jabbr 之类的框架可以自由地将任何对象添加到 `Resource` 中的 `AuthorizationHandlerContext` 属性，以便传递额外信息。

例如，MVC 在 [ 属性中传递 ](/dotnet/api/?term=AuthorizationFilterContext)AuthorizationFilterContext`Resource` 实例。 可以通过此属性访问 `HttpContext`、`RouteData` 以及 MVC 和 Razor 页面提供的所有其他内容。

对 `Resource` 属性的使用取决于框架。 使用 `Resource` 属性中的信息时，授权策略就会局限于特定的框架。 应使用 `Resource` 关键字来强制转换 `as` 属性，然后确认该强制转换是否成功，确保代码在其他框架上运行时不会崩溃并抛出`InvalidCastException` 异常：

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
