---
title: 迁移 ClaimsPrincipal.Current
author: mjrousos
description: 了解如何摆脱 ClaimsPrincipal.Current 检索当前经过身份验证的用户的标识和 ASP.NET Core 中的声明。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: 526cc3cf3a58a656e2a1b162cfaccacc7694dc51
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894784"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>迁移 ClaimsPrincipal.Current

在 ASP.NET 4.x 项目中，常见的做法是使用[ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)来检索当前身份验证的用户的标识和声明。 在 ASP.NET Core 中，不能再设置此属性。 取决于它的代码需要更新，以通过不同方式获取当前经过身份验证的用户的标识。

## <a name="context-specific-data-instead-of-static-data"></a>特定于上下文的数据，而不是静态数据

使用 ASP.NET Core，这两者的值时`ClaimsPrincipal.Current`和`Thread.CurrentPrincipal`未设置。 这些属性都表示静态状态，通常避免 ASP.NET Core。 相反，ASP.NET Core 体系结构是检索特定于上下文的服务集合 （如当前用户的标识） 的依赖关系 (使用其[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 模型)。 此外，`Thread.CurrentPrincipal`是线程静态的因此它可能不会持续某些异步方案中的更改 (并`ClaimsPrincipal.Current`只需调用`Thread.CurrentPrincipal`默认情况下)。

若要了解的问题线程类型的静态成员可能会导致在异步方案中，请考虑下面的代码段：

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

前面的示例代码集`Thread.CurrentPrincipal`，并检查其值之前和之后等待的异步调用。 `Thread.CurrentPrincipal` 特定于*线程*在其上设置，而且方法是可能会继续在另一个线程在等待后的执行。 因此，`Thread.CurrentPrincipal`存在时将首先检查，但在调用后为 null `await Task.Yield()`。

从应用程序的 DI 服务集合获取当前用户的标识太多可测试性，因为可以轻松地注入测试的标识。

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>检索当前用户在 ASP.NET Core 应用程序

有几个选项用于检索当前经过身份验证的用户的`ClaimsPrincipal`中代替了 ASP.NET Core `ClaimsPrincipal.Current`:

* **ControllerBase.User**。 MVC 控制器可以访问当前经过身份验证的用户使用其[用户](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user)属性。
* **HttpContext.User**。 有权访问当前组件`HttpContext`（中间件） 可以获取当前用户的`ClaimsPrincipal`从[HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)。
* **从调用方传入**。 而无需访问当前库`HttpContext`通常称为从控制器或中间件组件，并且能与当前用户的标识作为参数传递。
* **IHttpContextAccessor**。 正在迁移到 ASP.NET Core 项目可能太大，无法轻松地将当前用户的标识传递到所有必要的位置。 在这种情况下， [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)可用作一种解决方法。 `IHttpContextAccessor` 能够访问当前`HttpContext`（如果存在）。 将获取尚未尚未更新可以使用 ASP.NET Core DI 驱动体系结构的代码中的当前用户的标识的短期解决方案：

  * 请`IHttpContextAccessor`DI 容器通过调用中提供[AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793)中`Startup.ConfigureServices`。
  * 获取的实例`IHttpContextAccessor`在启动过程并将其存储在静态变量中。 实例将提供对以前已从静态属性检索当前用户的代码。
  * 检索当前用户的`ClaimsPrincipal`使用`HttpContextAccessor.HttpContext?.User`。 如果此代码使用的 HTTP 请求上下文之外`HttpContext`为 null。

最后一个选项，请使用`IHttpContextAccessor`实例存储在静态变量中，是 ASP.NET Core 原则 （静态依赖项首选注入依赖项） 相反。 计划最终检索`IHttpContextAccessor`改为实例从依赖关系注入。 静态帮助程序时可能会有用桥，不过，将之前使用的大型现有 ASP.NET 应用迁移`ClaimsPrincipal.Current`。
