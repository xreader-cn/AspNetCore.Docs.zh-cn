---
title: 从 ClaimsPrincipal 迁移
author: mjrousos
description: 了解如何从 ClaimsPrincipal 迁移到，以检索当前经过身份验证的用户的标识和 ASP.NET Core 中的声明。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: faa3db1a4b9cb7ff3fb54ec8a7caf21e8a9bfb7b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015122"
---
# <a name="migrate-from-claimsprincipalcurrent"></a><span data-ttu-id="eb2a3-103">从 ClaimsPrincipal 迁移</span><span class="sxs-lookup"><span data-stu-id="eb2a3-103">Migrate from ClaimsPrincipal.Current</span></span>

<span data-ttu-id="eb2a3-104">在 ASP.NET 4.x 项目中，通常使用[ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current)来检索当前经过身份验证的用户的标识和声明。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-104">In ASP.NET 4.x projects, it was common to use [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) to retrieve the current authenticated user's identity and claims.</span></span> <span data-ttu-id="eb2a3-105">在 ASP.NET Core 中，不再设置此属性。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-105">In ASP.NET Core, this property is no longer set.</span></span> <span data-ttu-id="eb2a3-106">根据需要更新的代码，可以通过不同的方式获取当前经过身份验证的用户的标识。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-106">Code that was depending on it needs to be updated to get the current authenticated user's identity through a different means.</span></span>

## <a name="context-specific-state-instead-of-static-state"></a><span data-ttu-id="eb2a3-107">上下文特定的状态而不是静态状态</span><span class="sxs-lookup"><span data-stu-id="eb2a3-107">Context-specific state instead of static state</span></span>

<span data-ttu-id="eb2a3-108">使用 ASP.NET Core 时， `ClaimsPrincipal.Current` 不会设置和的值 `Thread.CurrentPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-108">When using ASP.NET Core, the values of both `ClaimsPrincipal.Current` and `Thread.CurrentPrincipal` aren't set.</span></span> <span data-ttu-id="eb2a3-109">这些属性都表示静态状态，这 ASP.NET Core 通常会避免这种情况。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-109">These properties both represent static state, which ASP.NET Core generally avoids.</span></span> <span data-ttu-id="eb2a3-110">相反，ASP.NET Core 使用[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 来提供当前用户的标识等依赖项。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-110">Instead, ASP.NET Core uses [dependency injection](xref:fundamentals/dependency-injection) (DI) to provide dependencies such as the current user's identity.</span></span> <span data-ttu-id="eb2a3-111">从 DI 获取当前用户的标识也更易于测试，因为可以轻松注入测试标识。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-111">Getting the current user's identity from DI is more testable, too, since test identities can be easily injected.</span></span>

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a><span data-ttu-id="eb2a3-112">在 ASP.NET Core 应用程序中检索当前用户</span><span class="sxs-lookup"><span data-stu-id="eb2a3-112">Retrieve the current user in an ASP.NET Core app</span></span>

<span data-ttu-id="eb2a3-113">有几个选项可用于检索 ASP.NET Core 的当前已验证身份的用户的 `ClaimsPrincipal` 位置 `ClaimsPrincipal.Current` ：</span><span class="sxs-lookup"><span data-stu-id="eb2a3-113">There are several options for retrieving the current authenticated user's `ClaimsPrincipal` in ASP.NET Core in place of `ClaimsPrincipal.Current`:</span></span>

* <span data-ttu-id="eb2a3-114">**ControllerBase**。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-114">**ControllerBase.User**.</span></span> <span data-ttu-id="eb2a3-115">MVC 控制器可以使用[用户的用户](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user)属性来访问当前已经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-115">MVC controllers can access the current authenticated user with their [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) property.</span></span>
* <span data-ttu-id="eb2a3-116">**Httpcontext.current**。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-116">**HttpContext.User**.</span></span> <span data-ttu-id="eb2a3-117">有权访问当前 `HttpContext` (中间件的组件，例如) 可以从 HttpContext 获取当前用户 `ClaimsPrincipal` 的[HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-117">Components with access to the current `HttpContext` (middleware, for example) can get the current user's `ClaimsPrincipal` from [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).</span></span>
* <span data-ttu-id="eb2a3-118">**从调用方传入**。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-118">**Passed in from caller**.</span></span> <span data-ttu-id="eb2a3-119">`HttpContext`通常从控制器或中间件组件调用不具有当前访问权限的库，并且可以将当前用户的标识作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-119">Libraries without access to the current `HttpContext` are often called from controllers or middleware components and can have the current user's identity passed as an argument.</span></span>
* <span data-ttu-id="eb2a3-120">**IHttpContextAccessor**。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-120">**IHttpContextAccessor**.</span></span> <span data-ttu-id="eb2a3-121">正在迁移到 ASP.NET Core 的项目可能太大，无法轻松地将当前用户的标识传递到所有必要的位置。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-121">The project being migrated to ASP.NET Core may be too large to easily pass the current user's identity to all necessary locations.</span></span> <span data-ttu-id="eb2a3-122">在这种情况下，可以使用[IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor)作为一种解决方法。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-122">In such cases, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) can be used as a workaround.</span></span> <span data-ttu-id="eb2a3-123">`IHttpContextAccessor`如果) 存在，则能够访问当前 `HttpContext` (。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-123">`IHttpContextAccessor` is able to access the current `HttpContext` (if one exists).</span></span> <span data-ttu-id="eb2a3-124">如果正在使用 DI，请参阅 <xref:fundamentals/httpcontext> 。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-124">If DI is being used, see <xref:fundamentals/httpcontext>.</span></span> <span data-ttu-id="eb2a3-125">一种短期解决方案，用于在代码中获取当前用户的标识，但尚未更新为使用 ASP.NET Core 的 DI 驱动的体系结构：</span><span class="sxs-lookup"><span data-stu-id="eb2a3-125">A short-term solution to getting the current user's identity in code that hasn't yet been updated to work with ASP.NET Core's DI-driven architecture would be:</span></span>

  * <span data-ttu-id="eb2a3-126">`IHttpContextAccessor`在中调用[AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) ，使其在 DI 容器中可用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-126">Make `IHttpContextAccessor` available in the DI container by calling [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`.</span></span>
  * <span data-ttu-id="eb2a3-127">`IHttpContextAccessor`在启动过程中获取实例，并将其存储在静态变量中。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-127">Get an instance of `IHttpContextAccessor` during startup and store it in a static variable.</span></span> <span data-ttu-id="eb2a3-128">此实例可用于以前从静态属性中检索当前用户的代码。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-128">The instance is made available to code that was previously retrieving the current user from a static property.</span></span>
  * <span data-ttu-id="eb2a3-129">使用检索当前用户的 `ClaimsPrincipal` `HttpContextAccessor.HttpContext?.User` 。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-129">Retrieve the current user's `ClaimsPrincipal` using `HttpContextAccessor.HttpContext?.User`.</span></span> <span data-ttu-id="eb2a3-130">如果在 HTTP 请求的上下文之外使用此代码， `HttpContext` 则为 null。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-130">If this code is used outside of the context of an HTTP request, the `HttpContext` is null.</span></span>

<span data-ttu-id="eb2a3-131">最后一种方法是使用 `IHttpContextAccessor` 存储在静态变量中的实例，而不是首选向静态依赖项注入的依赖项的 ASP.NET Core 原则。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-131">The final option, using an `IHttpContextAccessor` instance stored in a static variable, is contrary to the ASP.NET Core principle of preferring injected dependencies to static dependencies.</span></span> <span data-ttu-id="eb2a3-132">计划最终 `IHttpContextAccessor` 从 DI 检索实例。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-132">Plan to eventually retrieve `IHttpContextAccessor` instances from DI instead.</span></span> <span data-ttu-id="eb2a3-133">但在迁移使用的大型现有 ASP.NET 应用程序时，静态帮助器可能是一个有用的桥 `ClaimsPrincipal.Current` 。</span><span class="sxs-lookup"><span data-stu-id="eb2a3-133">A static helper can be a useful bridge, though, when migrating large existing ASP.NET apps that use `ClaimsPrincipal.Current`.</span></span>
