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
# <a name="migrate-from-claimsprincipalcurrent"></a><span data-ttu-id="1c942-103">迁移 ClaimsPrincipal.Current</span><span class="sxs-lookup"><span data-stu-id="1c942-103">Migrate from ClaimsPrincipal.Current</span></span>

<span data-ttu-id="1c942-104">在 ASP.NET 4.x 项目中，常见的做法是使用[ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current)来检索当前身份验证的用户的标识和声明。</span><span class="sxs-lookup"><span data-stu-id="1c942-104">In ASP.NET 4.x projects, it was common to use [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) to retrieve the current authenticated user's identity and claims.</span></span> <span data-ttu-id="1c942-105">在 ASP.NET Core 中，不能再设置此属性。</span><span class="sxs-lookup"><span data-stu-id="1c942-105">In ASP.NET Core, this property is no longer set.</span></span> <span data-ttu-id="1c942-106">取决于它的代码需要更新，以通过不同方式获取当前经过身份验证的用户的标识。</span><span class="sxs-lookup"><span data-stu-id="1c942-106">Code that was depending on it needs to be updated to get the current authenticated user's identity through a different means.</span></span>

## <a name="context-specific-data-instead-of-static-data"></a><span data-ttu-id="1c942-107">特定于上下文的数据，而不是静态数据</span><span class="sxs-lookup"><span data-stu-id="1c942-107">Context-specific data instead of static data</span></span>

<span data-ttu-id="1c942-108">使用 ASP.NET Core，这两者的值时`ClaimsPrincipal.Current`和`Thread.CurrentPrincipal`未设置。</span><span class="sxs-lookup"><span data-stu-id="1c942-108">When using ASP.NET Core, the values of both `ClaimsPrincipal.Current` and `Thread.CurrentPrincipal` aren't set.</span></span> <span data-ttu-id="1c942-109">这些属性都表示静态状态，通常避免 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="1c942-109">These properties both represent static state, which ASP.NET Core generally avoids.</span></span> <span data-ttu-id="1c942-110">相反，ASP.NET Core 体系结构是检索特定于上下文的服务集合 （如当前用户的标识） 的依赖关系 (使用其[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 模型)。</span><span class="sxs-lookup"><span data-stu-id="1c942-110">Instead, ASP.NET Core's architecture is to retrieve dependencies (like the current user's identity) from context-specific service collections (using its [dependency injection](xref:fundamentals/dependency-injection) (DI) model).</span></span> <span data-ttu-id="1c942-111">此外，`Thread.CurrentPrincipal`是线程静态的因此它可能不会持续某些异步方案中的更改 (并`ClaimsPrincipal.Current`只需调用`Thread.CurrentPrincipal`默认情况下)。</span><span class="sxs-lookup"><span data-stu-id="1c942-111">What's more, `Thread.CurrentPrincipal` is thread static, so it may not persist changes in some asynchronous scenarios (and `ClaimsPrincipal.Current` just calls `Thread.CurrentPrincipal` by default).</span></span>

<span data-ttu-id="1c942-112">若要了解的问题线程类型的静态成员可能会导致在异步方案中，请考虑下面的代码段：</span><span class="sxs-lookup"><span data-stu-id="1c942-112">To understand the sorts of problems thread static members can lead to in asynchronous scenarios, consider the following code snippet:</span></span>

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

<span data-ttu-id="1c942-113">前面的示例代码集`Thread.CurrentPrincipal`，并检查其值之前和之后等待的异步调用。</span><span class="sxs-lookup"><span data-stu-id="1c942-113">The preceding sample code sets `Thread.CurrentPrincipal` and checks its value before and after awaiting an asynchronous call.</span></span> <span data-ttu-id="1c942-114">`Thread.CurrentPrincipal` 特定于*线程*在其上设置，而且方法是可能会继续在另一个线程在等待后的执行。</span><span class="sxs-lookup"><span data-stu-id="1c942-114">`Thread.CurrentPrincipal` is specific to the *thread* on which it's set, and the method is likely to resume execution on a different thread after the await.</span></span> <span data-ttu-id="1c942-115">因此，`Thread.CurrentPrincipal`存在时将首先检查，但在调用后为 null `await Task.Yield()`。</span><span class="sxs-lookup"><span data-stu-id="1c942-115">Consequently, `Thread.CurrentPrincipal` is present when it's first checked but is null after the call to `await Task.Yield()`.</span></span>

<span data-ttu-id="1c942-116">从应用程序的 DI 服务集合获取当前用户的标识太多可测试性，因为可以轻松地注入测试的标识。</span><span class="sxs-lookup"><span data-stu-id="1c942-116">Getting the current user's identity from the app's DI service collection is more testable, too, since test identities can be easily injected.</span></span>

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a><span data-ttu-id="1c942-117">检索当前用户在 ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="1c942-117">Retrieve the current user in an ASP.NET Core app</span></span>

<span data-ttu-id="1c942-118">有几个选项用于检索当前经过身份验证的用户的`ClaimsPrincipal`中代替了 ASP.NET Core `ClaimsPrincipal.Current`:</span><span class="sxs-lookup"><span data-stu-id="1c942-118">There are several options for retrieving the current authenticated user's `ClaimsPrincipal` in ASP.NET Core in place of `ClaimsPrincipal.Current`:</span></span>

* <span data-ttu-id="1c942-119">**ControllerBase.User**。</span><span class="sxs-lookup"><span data-stu-id="1c942-119">**ControllerBase.User**.</span></span> <span data-ttu-id="1c942-120">MVC 控制器可以访问当前经过身份验证的用户使用其[用户](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user)属性。</span><span class="sxs-lookup"><span data-stu-id="1c942-120">MVC controllers can access the current authenticated user with their [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) property.</span></span>
* <span data-ttu-id="1c942-121">**HttpContext.User**。</span><span class="sxs-lookup"><span data-stu-id="1c942-121">**HttpContext.User**.</span></span> <span data-ttu-id="1c942-122">有权访问当前组件`HttpContext`（中间件） 可以获取当前用户的`ClaimsPrincipal`从[HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)。</span><span class="sxs-lookup"><span data-stu-id="1c942-122">Components with access to the current `HttpContext` (middleware, for example) can get the current user's `ClaimsPrincipal` from [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).</span></span>
* <span data-ttu-id="1c942-123">**从调用方传入**。</span><span class="sxs-lookup"><span data-stu-id="1c942-123">**Passed in from caller**.</span></span> <span data-ttu-id="1c942-124">而无需访问当前库`HttpContext`通常称为从控制器或中间件组件，并且能与当前用户的标识作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="1c942-124">Libraries without access to the current `HttpContext` are often called from controllers or middleware components and can have the current user's identity passed as an argument.</span></span>
* <span data-ttu-id="1c942-125">**IHttpContextAccessor**。</span><span class="sxs-lookup"><span data-stu-id="1c942-125">**IHttpContextAccessor**.</span></span> <span data-ttu-id="1c942-126">正在迁移到 ASP.NET Core 项目可能太大，无法轻松地将当前用户的标识传递到所有必要的位置。</span><span class="sxs-lookup"><span data-stu-id="1c942-126">The project being migrated to ASP.NET Core may be too large to easily pass the current user's identity to all necessary locations.</span></span> <span data-ttu-id="1c942-127">在这种情况下， [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)可用作一种解决方法。</span><span class="sxs-lookup"><span data-stu-id="1c942-127">In such cases, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) can be used as a workaround.</span></span> <span data-ttu-id="1c942-128">`IHttpContextAccessor` 能够访问当前`HttpContext`（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="1c942-128">`IHttpContextAccessor` is able to access the current `HttpContext` (if one exists).</span></span> <span data-ttu-id="1c942-129">将获取尚未尚未更新可以使用 ASP.NET Core DI 驱动体系结构的代码中的当前用户的标识的短期解决方案：</span><span class="sxs-lookup"><span data-stu-id="1c942-129">A short-term solution to getting the current user's identity in code that hasn't yet been updated to work with ASP.NET Core's DI-driven architecture would be:</span></span>

  * <span data-ttu-id="1c942-130">请`IHttpContextAccessor`DI 容器通过调用中提供[AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793)中`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="1c942-130">Make `IHttpContextAccessor` available in the DI container by calling [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`.</span></span>
  * <span data-ttu-id="1c942-131">获取的实例`IHttpContextAccessor`在启动过程并将其存储在静态变量中。</span><span class="sxs-lookup"><span data-stu-id="1c942-131">Get an instance of `IHttpContextAccessor` during startup and store it in a static variable.</span></span> <span data-ttu-id="1c942-132">实例将提供对以前已从静态属性检索当前用户的代码。</span><span class="sxs-lookup"><span data-stu-id="1c942-132">The instance is made available to code that was previously retrieving the current user from a static property.</span></span>
  * <span data-ttu-id="1c942-133">检索当前用户的`ClaimsPrincipal`使用`HttpContextAccessor.HttpContext?.User`。</span><span class="sxs-lookup"><span data-stu-id="1c942-133">Retrieve the current user's `ClaimsPrincipal` using `HttpContextAccessor.HttpContext?.User`.</span></span> <span data-ttu-id="1c942-134">如果此代码使用的 HTTP 请求上下文之外`HttpContext`为 null。</span><span class="sxs-lookup"><span data-stu-id="1c942-134">If this code is used outside of the context of an HTTP request, the `HttpContext` is null.</span></span>

<span data-ttu-id="1c942-135">最后一个选项，请使用`IHttpContextAccessor`实例存储在静态变量中，是 ASP.NET Core 原则 （静态依赖项首选注入依赖项） 相反。</span><span class="sxs-lookup"><span data-stu-id="1c942-135">The final option, using an `IHttpContextAccessor` instance stored in a static variable, is contrary to ASP.NET Core principles (preferring injected dependencies to static dependencies).</span></span> <span data-ttu-id="1c942-136">计划最终检索`IHttpContextAccessor`改为实例从依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="1c942-136">Plan to eventually retrieve `IHttpContextAccessor` instances from dependency injection instead.</span></span> <span data-ttu-id="1c942-137">静态帮助程序时可能会有用桥，不过，将之前使用的大型现有 ASP.NET 应用迁移`ClaimsPrincipal.Current`。</span><span class="sxs-lookup"><span data-stu-id="1c942-137">A static helper can be a useful bridge, though, when migrating large existing ASP.NET apps that were previously using `ClaimsPrincipal.Current`.</span></span>
