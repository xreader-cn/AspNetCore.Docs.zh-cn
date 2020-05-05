---
title: 从 ClaimsPrincipal 迁移
author: mjrousos
description: 了解如何从 ClaimsPrincipal 迁移到，以检索当前经过身份验证的用户的标识和 ASP.NET Core 中的声明。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776085"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>从 ClaimsPrincipal 迁移

在 ASP.NET 4.x 项目中，通常使用[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current)来检索当前经过身份验证的用户的标识和声明。 在 ASP.NET Core 中，不再设置此属性。 根据需要更新的代码，可以通过不同的方式获取当前经过身份验证的用户的标识。

## <a name="context-specific-data-instead-of-static-data"></a>上下文特定的数据而不是静态数据

使用 ASP.NET Core 时，不会设置`ClaimsPrincipal.Current`和`Thread.CurrentPrincipal`的值。 这些属性都表示静态状态，这 ASP.NET Core 通常会避免这种情况。 相反，ASP.NET Core 的体系结构是从特定于上下文的服务集合（使用其[依赖项注入](xref:fundamentals/dependency-injection)（DI）模型）检索依赖项（如当前用户的标识）。 更多`Thread.CurrentPrincipal`是线程静态的，因此它可能不会在某些异步方案中持久保存更改`ClaimsPrincipal.Current` （默认`Thread.CurrentPrincipal`情况下，只需调用）。

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

前面的示例代码在`Thread.CurrentPrincipal`等待异步调用前后设置并检查其值。 `Thread.CurrentPrincipal`特定于在其上设置它的*线程*，并且该方法可能会在等待后继续在另一个线程上执行。 因此， `Thread.CurrentPrincipal`在第一次选中但在调用之后为空时，就`await Task.Yield()`存在。

从应用的 DI 服务集合获取当前用户的标识也更易于测试，因为可以轻松注入测试标识。

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>在 ASP.NET Core 应用程序中检索当前用户

有几个选项可用于检索 ASP.NET Core 的当前已`ClaimsPrincipal`验证身份的用户的`ClaimsPrincipal.Current`位置：

* **ControllerBase**。 MVC 控制器可以使用[用户的用户](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user)属性来访问当前已经过身份验证的用户。
* **Httpcontext.current**。 有权访问当前`HttpContext`的组件（例如中间件）可以从[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)获取当前用户的`ClaimsPrincipal` 。
* **从调用方传入**。 通常从控制器或中间件`HttpContext`组件调用不具有当前访问权限的库，并且可以将当前用户的标识作为参数传递。
* **IHttpContextAccessor**。 正在迁移到 ASP.NET Core 的项目可能太大，无法轻松地将当前用户的标识传递到所有必要的位置。 在这种情况下，可以使用[IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)作为一种解决方法。 `IHttpContextAccessor`可以访问当前`HttpContext`的（如果存在）。 如果正在使用 DI，请参阅<xref:fundamentals/httpcontext>。 一种短期解决方案，用于在代码中获取当前用户的标识，但尚未更新为使用 ASP.NET Core 的 DI 驱动的体系结构：

  * 在`IHttpContextAccessor`中`Startup.ConfigureServices`调用[ADDHTTPCONTEXTACCESSOR](https://github.com/aspnet/Hosting/issues/793) ，使其在 DI 容器中可用。
  * 在启动`IHttpContextAccessor`过程中获取实例，并将其存储在静态变量中。 此实例可用于以前从静态属性中检索当前用户的代码。
  * 使用`HttpContextAccessor.HttpContext?.User`检索当前用户的`ClaimsPrincipal` 。 如果在 HTTP 请求的上下文之外使用此代码， `HttpContext`则为 null。

最后一种方法是使用`IHttpContextAccessor`存储在静态变量中的实例，这与 ASP.NET Core 原则（喜欢向静态依赖项注入的依赖项）相反。 计划最终从依赖`IHttpContextAccessor`关系注入检索实例。 但是，在迁移以前使用`ClaimsPrincipal.Current`的大型现有 ASP.NET 应用程序时，静态帮助器可能是一个有用的桥。
