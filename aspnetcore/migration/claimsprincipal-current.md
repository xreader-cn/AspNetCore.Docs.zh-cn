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
# <a name="migrate-from-claimsprincipalcurrent"></a><span data-ttu-id="4a8fc-103">从 ClaimsPrincipal 迁移</span><span class="sxs-lookup"><span data-stu-id="4a8fc-103">Migrate from ClaimsPrincipal.Current</span></span>

<span data-ttu-id="4a8fc-104">在 ASP.NET 4.x 项目中，通常使用[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current)来检索当前经过身份验证的用户的标识和声明。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-104">In ASP.NET 4.x projects, it was common to use [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) to retrieve the current authenticated user's identity and claims.</span></span> <span data-ttu-id="4a8fc-105">在 ASP.NET Core 中，不再设置此属性。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-105">In ASP.NET Core, this property is no longer set.</span></span> <span data-ttu-id="4a8fc-106">根据需要更新的代码，可以通过不同的方式获取当前经过身份验证的用户的标识。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-106">Code that was depending on it needs to be updated to get the current authenticated user's identity through a different means.</span></span>

## <a name="context-specific-data-instead-of-static-data"></a><span data-ttu-id="4a8fc-107">上下文特定的数据而不是静态数据</span><span class="sxs-lookup"><span data-stu-id="4a8fc-107">Context-specific data instead of static data</span></span>

<span data-ttu-id="4a8fc-108">使用 ASP.NET Core 时，不会设置 `ClaimsPrincipal.Current` 和 `Thread.CurrentPrincipal` 的值。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-108">When using ASP.NET Core, the values of both `ClaimsPrincipal.Current` and `Thread.CurrentPrincipal` aren't set.</span></span> <span data-ttu-id="4a8fc-109">这些属性都表示静态状态，这 ASP.NET Core 通常会避免这种情况。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-109">These properties both represent static state, which ASP.NET Core generally avoids.</span></span> <span data-ttu-id="4a8fc-110">相反，ASP.NET Core 的体系结构是从特定于上下文的服务集合（使用其[依赖项注入](xref:fundamentals/dependency-injection)（DI）模型）检索依赖项（如当前用户的标识）。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-110">Instead, ASP.NET Core's architecture is to retrieve dependencies (like the current user's identity) from context-specific service collections (using its [dependency injection](xref:fundamentals/dependency-injection) (DI) model).</span></span> <span data-ttu-id="4a8fc-111">此外，`Thread.CurrentPrincipal` 是线程静态的，因此它可能不会在某些异步方案中持久保存更改（`ClaimsPrincipal.Current` 只是在默认情况下 `Thread.CurrentPrincipal` 调用）。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-111">What's more, `Thread.CurrentPrincipal` is thread static, so it may not persist changes in some asynchronous scenarios (and `ClaimsPrincipal.Current` just calls `Thread.CurrentPrincipal` by default).</span></span>

<span data-ttu-id="4a8fc-112">若要了解在异步方案中线程静态成员可能导致的问题，请考虑以下代码片段：</span><span class="sxs-lookup"><span data-stu-id="4a8fc-112">To understand the sorts of problems thread static members can lead to in asynchronous scenarios, consider the following code snippet:</span></span>

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

<span data-ttu-id="4a8fc-113">前面的示例代码将 `Thread.CurrentPrincipal`，并在等待异步调用之前和之后检查其值。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-113">The preceding sample code sets `Thread.CurrentPrincipal` and checks its value before and after awaiting an asynchronous call.</span></span> <span data-ttu-id="4a8fc-114">`Thread.CurrentPrincipal` 特定于设置了它的*线程*，并且该方法可能会在等待后恢复到其他线程上执行。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-114">`Thread.CurrentPrincipal` is specific to the *thread* on which it's set, and the method is likely to resume execution on a different thread after the await.</span></span> <span data-ttu-id="4a8fc-115">因此，`Thread.CurrentPrincipal` 在首次检查时存在，但在调用 `await Task.Yield()`之后为 null。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-115">Consequently, `Thread.CurrentPrincipal` is present when it's first checked but is null after the call to `await Task.Yield()`.</span></span>

<span data-ttu-id="4a8fc-116">从应用的 DI 服务集合获取当前用户的标识也更易于测试，因为可以轻松注入测试标识。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-116">Getting the current user's identity from the app's DI service collection is more testable, too, since test identities can be easily injected.</span></span>

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a><span data-ttu-id="4a8fc-117">在 ASP.NET Core 应用程序中检索当前用户</span><span class="sxs-lookup"><span data-stu-id="4a8fc-117">Retrieve the current user in an ASP.NET Core app</span></span>

<span data-ttu-id="4a8fc-118">有几个选项可用于检索 ASP.NET Core 中当前已经过身份验证的用户的 `ClaimsPrincipal`，以代替 `ClaimsPrincipal.Current`：</span><span class="sxs-lookup"><span data-stu-id="4a8fc-118">There are several options for retrieving the current authenticated user's `ClaimsPrincipal` in ASP.NET Core in place of `ClaimsPrincipal.Current`:</span></span>

* <span data-ttu-id="4a8fc-119">**ControllerBase**。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-119">**ControllerBase.User**.</span></span> <span data-ttu-id="4a8fc-120">MVC 控制器可以使用[用户的用户](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user)属性来访问当前已经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-120">MVC controllers can access the current authenticated user with their [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) property.</span></span>
* <span data-ttu-id="4a8fc-121">**Httpcontext.current**。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-121">**HttpContext.User**.</span></span> <span data-ttu-id="4a8fc-122">有权访问当前 `HttpContext` （例如中间件）的组件可以从[HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)获取当前用户的 `ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-122">Components with access to the current `HttpContext` (middleware, for example) can get the current user's `ClaimsPrincipal` from [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).</span></span>
* <span data-ttu-id="4a8fc-123">**从调用方传入**。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-123">**Passed in from caller**.</span></span> <span data-ttu-id="4a8fc-124">通常从控制器或中间件组件调用没有访问当前 `HttpContext` 的库，并且可以将当前用户的标识作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-124">Libraries without access to the current `HttpContext` are often called from controllers or middleware components and can have the current user's identity passed as an argument.</span></span>
* <span data-ttu-id="4a8fc-125">**IHttpContextAccessor**。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-125">**IHttpContextAccessor**.</span></span> <span data-ttu-id="4a8fc-126">正在迁移到 ASP.NET Core 的项目可能太大，无法轻松地将当前用户的标识传递到所有必要的位置。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-126">The project being migrated to ASP.NET Core may be too large to easily pass the current user's identity to all necessary locations.</span></span> <span data-ttu-id="4a8fc-127">在这种情况下，可以使用[IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)作为一种解决方法。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-127">In such cases, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) can be used as a workaround.</span></span> <span data-ttu-id="4a8fc-128">`IHttpContextAccessor` 可以访问当前 `HttpContext` （如果存在）。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-128">`IHttpContextAccessor` is able to access the current `HttpContext` (if one exists).</span></span> <span data-ttu-id="4a8fc-129">如果正在使用 DI，请参阅 <xref:fundamentals/httpcontext>。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-129">If DI is being used, see <xref:fundamentals/httpcontext>.</span></span> <span data-ttu-id="4a8fc-130">一种短期解决方案，用于在代码中获取当前用户的标识，但尚未更新为使用 ASP.NET Core 的 DI 驱动的体系结构：</span><span class="sxs-lookup"><span data-stu-id="4a8fc-130">A short-term solution to getting the current user's identity in code that hasn't yet been updated to work with ASP.NET Core's DI-driven architecture would be:</span></span>

  * <span data-ttu-id="4a8fc-131">通过在 `Startup.ConfigureServices`中调用[AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) ，使 `IHttpContextAccessor` 在 DI 容器中可用。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-131">Make `IHttpContextAccessor` available in the DI container by calling [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`.</span></span>
  * <span data-ttu-id="4a8fc-132">在启动过程中获取 `IHttpContextAccessor` 的实例，并将其存储在静态变量中。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-132">Get an instance of `IHttpContextAccessor` during startup and store it in a static variable.</span></span> <span data-ttu-id="4a8fc-133">此实例可用于以前从静态属性中检索当前用户的代码。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-133">The instance is made available to code that was previously retrieving the current user from a static property.</span></span>
  * <span data-ttu-id="4a8fc-134">使用 `HttpContextAccessor.HttpContext?.User`检索当前用户的 `ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-134">Retrieve the current user's `ClaimsPrincipal` using `HttpContextAccessor.HttpContext?.User`.</span></span> <span data-ttu-id="4a8fc-135">如果在 HTTP 请求的上下文之外使用此代码，则 `HttpContext` 为 null。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-135">If this code is used outside of the context of an HTTP request, the `HttpContext` is null.</span></span>

<span data-ttu-id="4a8fc-136">最后一种方法是使用存储在静态变量中的 `IHttpContextAccessor` 实例，这与 ASP.NET Core 原则（喜欢向静态依赖项的注入的依赖项）相反。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-136">The final option, using an `IHttpContextAccessor` instance stored in a static variable, is contrary to ASP.NET Core principles (preferring injected dependencies to static dependencies).</span></span> <span data-ttu-id="4a8fc-137">改为计划最终从依赖关系注入检索 `IHttpContextAccessor` 实例。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-137">Plan to eventually retrieve `IHttpContextAccessor` instances from dependency injection instead.</span></span> <span data-ttu-id="4a8fc-138">但是，在迁移以前使用 `ClaimsPrincipal.Current`的大型现有 ASP.NET 应用时，静态帮助器可能是一个有用的桥。</span><span class="sxs-lookup"><span data-stu-id="4a8fc-138">A static helper can be a useful bridge, though, when migrating large existing ASP.NET apps that were previously using `ClaimsPrincipal.Current`.</span></span>
