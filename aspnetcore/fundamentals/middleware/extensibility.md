---
title: ASP.NET Core 中基于工厂的中间件激活
author: guardrex
description: 了解如何在 ASP.NET Core 中通过基于工厂的激活实现使用强类型中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 9305616ce3f2ef49cf9dfcab719f673c0fb4b51e
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809161"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="49031-103">ASP.NET Core 中基于工厂的中间件激活</span><span class="sxs-lookup"><span data-stu-id="49031-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="49031-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="49031-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="49031-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> 是[中间件](xref:fundamentals/middleware/index)激活的扩展点。</span><span class="sxs-lookup"><span data-stu-id="49031-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="49031-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 扩展方法检查中间件的已注册类型是否实现 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="49031-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="49031-107">如果是，则使用在容器中注册的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实例来解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 实现，而不使用基于约定的中间件激活逻辑。</span><span class="sxs-lookup"><span data-stu-id="49031-107">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="49031-108">中间件在应用的服务容器中注册为[作用域或瞬态](xref:fundamentals/dependency-injection#service-lifetimes)服务。</span><span class="sxs-lookup"><span data-stu-id="49031-108">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="49031-109">优点：</span><span class="sxs-lookup"><span data-stu-id="49031-109">Benefits:</span></span>

* <span data-ttu-id="49031-110">按客户端请求（作用域服务的注入）激活</span><span class="sxs-lookup"><span data-stu-id="49031-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="49031-111">让中间件强类型化</span><span class="sxs-lookup"><span data-stu-id="49031-111">Strong typing of middleware</span></span>

<span data-ttu-id="49031-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> 按客户端请求（连接）激活，因此作用域服务可以注入到中间件的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="49031-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="49031-113">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="49031-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="49031-114">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="49031-114">IMiddleware</span></span>

<span data-ttu-id="49031-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="49031-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="49031-116">[InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 方法处理请求，并返回代表中间件执行的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="49031-116">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="49031-117">使用约定激活的中间件：</span><span class="sxs-lookup"><span data-stu-id="49031-117">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="49031-118">使用 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> 激活的中间件：</span><span class="sxs-lookup"><span data-stu-id="49031-118">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="49031-119">程序会为中间件创建扩展：</span><span class="sxs-lookup"><span data-stu-id="49031-119">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="49031-120">无法通过 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 将对象传递给工厂激活的中间件：</span><span class="sxs-lookup"><span data-stu-id="49031-120">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="49031-121">将工厂激活的中间件添加到 `Startup.ConfigureServices` 的内置容器中：</span><span class="sxs-lookup"><span data-stu-id="49031-121">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="49031-122">两个中间件均在 `Startup.Configure` 的请求处理管道中注册：</span><span class="sxs-lookup"><span data-stu-id="49031-122">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="49031-123">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="49031-123">IMiddlewareFactory</span></span>

<span data-ttu-id="49031-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。</span><span class="sxs-lookup"><span data-stu-id="49031-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="49031-125">中间件工厂实现在容器中注册为作用域服务。</span><span class="sxs-lookup"><span data-stu-id="49031-125">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="49031-126">可在 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 包中找到默认的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实现（即 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>）。</span><span class="sxs-lookup"><span data-stu-id="49031-126">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49031-127">其他资源</span><span class="sxs-lookup"><span data-stu-id="49031-127">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
