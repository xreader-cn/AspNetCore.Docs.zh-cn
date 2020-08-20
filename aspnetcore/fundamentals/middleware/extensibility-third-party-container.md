---
title: 使用 ASP.NET Core 中的第三方容器激活中间件
author: rick-anderson
description: 了解如何在基于工厂的激活和 ASP.NET Core 中的第三方容器中使用强类型中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: e0ebcd46e6b506b7b2172f087f12773910ea4d9c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634860"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="1b940-103">使用 ASP.NET Core 中的第三方容器激活中间件</span><span class="sxs-lookup"><span data-stu-id="1b940-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b940-104">本文演示如何使用 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 和 <xref:Microsoft.AspNetCore.Http.IMiddleware> 作为使用第三方容器激活[中间件](xref:fundamentals/middleware/index)的可扩展点。</span><span class="sxs-lookup"><span data-stu-id="1b940-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="1b940-105">有关 `IMiddlewareFactory` 和 `IMiddleware` 的入门信息，请参阅 <xref:fundamentals/middleware/extensibility>。</span><span class="sxs-lookup"><span data-stu-id="1b940-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="1b940-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1b940-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1b940-107">示例应用演示了使用 `IMiddlewareFactory`、`SimpleInjectorMiddlewareFactory` 实现激活的中间件。</span><span class="sxs-lookup"><span data-stu-id="1b940-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="1b940-108">此示例使用 [Simple Injector](https://simpleinjector.org) 依赖项注入 (DI) 容器。</span><span class="sxs-lookup"><span data-stu-id="1b940-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="1b940-109">此示例的中间件实现记录了查询字符串参数 (`key`) 提供的值。</span><span class="sxs-lookup"><span data-stu-id="1b940-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="1b940-110">中间件使用插入的数据库上下文（有作用域的服务）将查询字符串值记录在内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="1b940-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="1b940-111">此示例应用仅出于演示目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。</span><span class="sxs-lookup"><span data-stu-id="1b940-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="1b940-112">不认可使用 Simple Injector。</span><span class="sxs-lookup"><span data-stu-id="1b940-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="1b940-113">Simple Injector 文档中描述的中间件激活方法和 Simple Injector 维护人员推荐的 GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="1b940-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="1b940-114">有关详细信息，请参阅 [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)和 [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)。</span><span class="sxs-lookup"><span data-stu-id="1b940-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="1b940-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="1b940-115">IMiddlewareFactory</span></span>

<span data-ttu-id="1b940-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。</span><span class="sxs-lookup"><span data-stu-id="1b940-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="1b940-117">在示例应用中，实现了中间件工厂以创建 `SimpleInjectorActivatedMiddleware` 实例。</span><span class="sxs-lookup"><span data-stu-id="1b940-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="1b940-118">中间件工厂使用 Simple Injector 容器来解析中间件：</span><span class="sxs-lookup"><span data-stu-id="1b940-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="1b940-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="1b940-119">IMiddleware</span></span>

<span data-ttu-id="1b940-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="1b940-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="1b940-121">由 `IMiddlewareFactory` 实现 (Middleware/SimpleInjectorActivatedMiddleware.cs) 激活的中间件  ：</span><span class="sxs-lookup"><span data-stu-id="1b940-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="1b940-122">为中间件创建扩展 (Middleware/MiddlewareExtensions.cs)  ：</span><span class="sxs-lookup"><span data-stu-id="1b940-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="1b940-123">`Startup.ConfigureServices` 必须执行多项任务：</span><span class="sxs-lookup"><span data-stu-id="1b940-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="1b940-124">设置 Simple Injector 容器。</span><span class="sxs-lookup"><span data-stu-id="1b940-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="1b940-125">注册工厂和中间件。</span><span class="sxs-lookup"><span data-stu-id="1b940-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="1b940-126">使 Simple Injector 容器提供应用的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="1b940-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1b940-127">中间件在 `Startup.Configure` 的请求处理管道中注册：</span><span class="sxs-lookup"><span data-stu-id="1b940-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1b940-128">本文演示如何使用 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 和 <xref:Microsoft.AspNetCore.Http.IMiddleware> 作为使用第三方容器激活[中间件](xref:fundamentals/middleware/index)的可扩展点。</span><span class="sxs-lookup"><span data-stu-id="1b940-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="1b940-129">有关 `IMiddlewareFactory` 和 `IMiddleware` 的入门信息，请参阅 <xref:fundamentals/middleware/extensibility>。</span><span class="sxs-lookup"><span data-stu-id="1b940-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="1b940-130">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1b940-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1b940-131">示例应用演示了使用 `IMiddlewareFactory`、`SimpleInjectorMiddlewareFactory` 实现激活的中间件。</span><span class="sxs-lookup"><span data-stu-id="1b940-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="1b940-132">此示例使用 [Simple Injector](https://simpleinjector.org) 依赖项注入 (DI) 容器。</span><span class="sxs-lookup"><span data-stu-id="1b940-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="1b940-133">此示例的中间件实现记录了查询字符串参数 (`key`) 提供的值。</span><span class="sxs-lookup"><span data-stu-id="1b940-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="1b940-134">中间件使用插入的数据库上下文（有作用域的服务）将查询字符串值记录在内存中数据库。</span><span class="sxs-lookup"><span data-stu-id="1b940-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="1b940-135">此示例应用仅出于演示目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。</span><span class="sxs-lookup"><span data-stu-id="1b940-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="1b940-136">不认可使用 Simple Injector。</span><span class="sxs-lookup"><span data-stu-id="1b940-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="1b940-137">Simple Injector 文档中描述的中间件激活方法和 Simple Injector 维护人员推荐的 GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="1b940-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="1b940-138">有关详细信息，请参阅 [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)和 [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)。</span><span class="sxs-lookup"><span data-stu-id="1b940-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="1b940-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="1b940-139">IMiddlewareFactory</span></span>

<span data-ttu-id="1b940-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。</span><span class="sxs-lookup"><span data-stu-id="1b940-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="1b940-141">在示例应用中，实现了中间件工厂以创建 `SimpleInjectorActivatedMiddleware` 实例。</span><span class="sxs-lookup"><span data-stu-id="1b940-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="1b940-142">中间件工厂使用 Simple Injector 容器来解析中间件：</span><span class="sxs-lookup"><span data-stu-id="1b940-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="1b940-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="1b940-143">IMiddleware</span></span>

<span data-ttu-id="1b940-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="1b940-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="1b940-145">由 `IMiddlewareFactory` 实现 (Middleware/SimpleInjectorActivatedMiddleware.cs) 激活的中间件  ：</span><span class="sxs-lookup"><span data-stu-id="1b940-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="1b940-146">为中间件创建扩展 (Middleware/MiddlewareExtensions.cs)  ：</span><span class="sxs-lookup"><span data-stu-id="1b940-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="1b940-147">`Startup.ConfigureServices` 必须执行多项任务：</span><span class="sxs-lookup"><span data-stu-id="1b940-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="1b940-148">设置 Simple Injector 容器。</span><span class="sxs-lookup"><span data-stu-id="1b940-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="1b940-149">注册工厂和中间件。</span><span class="sxs-lookup"><span data-stu-id="1b940-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="1b940-150">使 Simple Injector 容器提供应用的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="1b940-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1b940-151">中间件在 `Startup.Configure` 的请求处理管道中注册：</span><span class="sxs-lookup"><span data-stu-id="1b940-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1b940-152">其他资源</span><span class="sxs-lookup"><span data-stu-id="1b940-152">Additional resources</span></span>

* [<span data-ttu-id="1b940-153">中间件</span><span class="sxs-lookup"><span data-stu-id="1b940-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="1b940-154">基于工厂的中间件激活</span><span class="sxs-lookup"><span data-stu-id="1b940-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="1b940-155">Simple Injector GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="1b940-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="1b940-156">Simple Injector 文档</span><span class="sxs-lookup"><span data-stu-id="1b940-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
