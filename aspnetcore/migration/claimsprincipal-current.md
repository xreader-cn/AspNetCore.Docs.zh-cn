---
title: 从 ClaimsPrincipal 迁移
author: mjrousos
description: 了解如何从 ClaimsPrincipal 迁移到，以检索当前经过身份验证的用户的标识和 ASP.NET Core 中的声明。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: f7472f5b851d3869da3d26b881e276ce4ca004fb
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115969"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>从 ClaimsPrincipal 迁移

在 ASP.NET 4.x 项目中，通常使用[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current)来检索当前经过身份验证的用户的标识和声明。 在 ASP.NET Core 中，不再设置此属性。 根据需要更新的代码，可以通过不同的方式获取当前经过身份验证的用户的标识。

## <a name="context-specific-data-instead-of-static-data"></a>上下文特定的数据而不是静态数据

使用 ASP.NET Core 时，不会设置 `ClaimsPrincipal.Current` 和 `Thread.CurrentPrincipal` 的值。 这些属性都表示静态状态，这 ASP.NET Core 通常会避免这种情况。 相反，ASP.NET Core 的体系结构是从特定于上下文的服务集合（使用其[依赖项注入](xref:fundamentals/dependency-injection)（DI）模型）检索依赖项（如当前用户的标识）。 此外，`Thread.CurrentPrincipal` 是线程静态的，因此它可能不会在某些异步方案中持久保存更改（`ClaimsPrincipal.Current` 只是在默认情况下 `Thread.CurrentPrincipal` 调用）。

若要了解在异步方案中线程静态成员可能导致的问题，请考虑以下代码片段：

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

前面的示例代码将 `Thread.CurrentPrincipal`，并在等待异步调用之前和之后检查其值。 `Thread.CurrentPrincipal` 特定于设置了它的*线程*，并且该方法可能会在等待后恢复到其他线程上执行。 因此，`Thread.CurrentPrincipal` 在首次检查时存在，但在调用 `await Task.Yield()`之后为 null。

从应用的 DI 服务集合获取当前用户的标识也更易于测试，因为可以轻松注入测试标识。

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>在 ASP.NET Core 应用程序中检索当前用户

有几个选项可用于检索 ASP.NET Core 中当前已经过身份验证的用户的 `ClaimsPrincipal`，以代替 `ClaimsPrincipal.Current`：

* **ControllerBase**。 MVC 控制器可以使用[用户的用户](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user)属性来访问当前已经过身份验证的用户。
* **Httpcontext.current**。 有权访问当前 `HttpContext` （例如中间件）的组件可以从[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)获取当前用户的 `ClaimsPrincipal`。
* **从调用方传入**。 通常从控制器或中间件组件调用没有访问当前 `HttpContext` 的库，并且可以将当前用户的标识作为参数传递。
* **IHttpContextAccessor**。 正在迁移到 ASP.NET Core 的项目可能太大，无法轻松地将当前用户的标识传递到所有必要的位置。 在这种情况下，可以使用[IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)作为一种解决方法。 `IHttpContextAccessor` 可以访问当前 `HttpContext` （如果存在）。 如果正在使用 DI，请参阅 <xref:fundamentals/httpcontext>。 一种短期解决方案，用于在代码中获取当前用户的标识，但尚未更新为使用 ASP.NET Core 的 DI 驱动的体系结构：

  * 通过在 `Startup.ConfigureServices`中调用[AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) ，使 `IHttpContextAccessor` 在 DI 容器中可用。
  * 在启动过程中获取 `IHttpContextAccessor` 的实例，并将其存储在静态变量中。 此实例可用于以前从静态属性中检索当前用户的代码。
  * 使用 `HttpContextAccessor.HttpContext?.User`检索当前用户的 `ClaimsPrincipal`。 如果在 HTTP 请求的上下文之外使用此代码，则 `HttpContext` 为 null。

最后一种方法是使用存储在静态变量中的 `IHttpContextAccessor` 实例，这与 ASP.NET Core 原则（喜欢向静态依赖项的注入的依赖项）相反。 改为计划最终从依赖关系注入检索 `IHttpContextAccessor` 实例。 但是，在迁移以前使用 `ClaimsPrincipal.Current`的大型现有 ASP.NET 应用时，静态帮助器可能是一个有用的桥。
