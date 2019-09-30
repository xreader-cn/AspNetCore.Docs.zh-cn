---
title: 处理 ASP.NET Core Web API 中的错误
author: pranavkm
description: 了解 ASP.NET Core Web API 的错误处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/25/2019
uid: web-api/handle-errors
ms.openlocfilehash: 9c5dd2f89e7351f386d1f0633c831952dc58e568
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278731"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="a2ca0-103">处理 ASP.NET Core Web API 中的错误</span><span class="sxs-lookup"><span data-stu-id="a2ca0-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="a2ca0-104">本文介绍如何处理和自定义 ASP.NET Core Web API 的错误处理。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="a2ca0-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a2ca0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a2ca0-106">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="a2ca0-106">Developer Exception Page</span></span>

<span data-ttu-id="a2ca0-107">[开发人员异常页](xref:fundamentals/error-handling)是一种用于获取服务器错误详细堆栈跟踪的有用工具。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span>

<span data-ttu-id="a2ca0-108">如果客户端不接受 HTML 格式的输出，则开发人员异常页将显示纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-108">The Developer Exception Page displays a plain-text response if the client doesn't accept HTML-formatted output.</span></span> <span data-ttu-id="a2ca0-109">例如:</span><span class="sxs-lookup"><span data-stu-id="a2ca0-109">For example:</span></span>

```
> curl https://localhost:5001/weatherforecast
System.ArgumentException: count
   at errorhandling.Controllers.WeatherForecastController.Get(Int32 x) in D:\work\Samples\samples\aspnetcore\mvc\errorhandling\Controllers\WeatherForecastController.cs:line 35
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
...
```

> [!WARNING]
> <span data-ttu-id="a2ca0-110">仅当应用程序在开发环境中运行时才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-110">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a2ca0-111">否则当应用程序在生产环境中运行时，详细的异常信息会向公众泄露</span><span class="sxs-lookup"><span data-stu-id="a2ca0-111">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a2ca0-112">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-112">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="a2ca0-113">异常处理程序</span><span class="sxs-lookup"><span data-stu-id="a2ca0-113">Exception handler</span></span>

<span data-ttu-id="a2ca0-114">在非开发环境中，可使用[异常处理中间件](xref:fundamentals/error-handling)来生成错误负载：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-114">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="a2ca0-115">在 `Startup.Configure` 中，调用 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 以使用中间件：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-115">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="a2ca0-116">配置控制器操作以响应 `/error` 路由：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-116">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="a2ca0-117">前面的 `Error` 操作向客户端发送与 [RFC7807](https://tools.ietf.org/html/rfc7807) 兼容的负载。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-117">The preceding `Error` action sends an [RFC7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="a2ca0-118">异常处理中间件还可以在本地开发环境中提供更详细的内容协商输出。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-118">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="a2ca0-119">使用以下步骤在开发和生产环境中生成一致的负载格式：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-119">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="a2ca0-120">在 `Startup.Configure` 中，注册特定于环境的异常处理中间件实例：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-120">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="a2ca0-121">在上述代码中，通过以下方法注册了中间件：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-121">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="a2ca0-122">开发环境中 `/error-local-development` 的路由。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-122">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="a2ca0-123">非开发环境中 `/error` 的路由。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-123">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="a2ca0-124">将属性路由应用于控制器操作：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-124">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="a2ca0-125">使用异常来修改响应</span><span class="sxs-lookup"><span data-stu-id="a2ca0-125">Use exceptions to modify the response</span></span>

<span data-ttu-id="a2ca0-126">可以从控制器之外修改响应的内容。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-126">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="a2ca0-127">在 ASP.NET 4.x Web API 中，执行此操作的一种方法是使用 <xref:System.Web.Http.HttpResponseException> 类型。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-127">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="a2ca0-128">ASP.NET Core 不包括等效类型。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-128">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="a2ca0-129">可以使用以下步骤来添加对 `HttpResponseException` 的支持：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-129">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="a2ca0-130">创建名为 `HttpResponseException` 的已知异常类型：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-130">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="a2ca0-131">创建名为 `HttpResponseExceptionFilter` 的操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-131">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="a2ca0-132">在 `Startup.ConfigureServices` 中，将操作筛选器添加到筛选器集合：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-132">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="a2ca0-133">验证失败错误响应</span><span class="sxs-lookup"><span data-stu-id="a2ca0-133">Validation failure error response</span></span>

<span data-ttu-id="a2ca0-134">对于 Web API 控制器，如果模型验证失败，MVC 将使用 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 响应类型做出响应。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-134">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="a2ca0-135">MVC 使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> 的结果来构造验证失败的错误响应。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-135">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="a2ca0-136">下面的示例使用工厂在 `Startup.ConfigureServices` 中将默认响应类型更改为 <xref:Microsoft.AspNetCore.Mvc.SerializableError>：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-136">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="a2ca0-137">客户端错误响应</span><span class="sxs-lookup"><span data-stu-id="a2ca0-137">Client error response</span></span>

<span data-ttu-id="a2ca0-138">错误结果定义为具有 HTTP 状态代码 400 或更高的结果。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-138">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="a2ca0-139">对于 Web API 控制器，MVC 会将错误结果转换为具有 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 的结果。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-139">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a2ca0-140">可以通过以下方式之一配置错误响应：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-140">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="a2ca0-141">实现 ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="a2ca0-141">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="a2ca0-142">使用 ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="a2ca0-142">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="a2ca0-143">实现 ProblemDetailsFactory</span><span class="sxs-lookup"><span data-stu-id="a2ca0-143">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="a2ca0-144">MVC 使用 `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` 生成 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 和 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> 的所有实例。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-144">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="a2ca0-145">这包括客户端错误响应、验证失败错误响应以及 `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> 帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-145">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="a2ca0-146">若要自定义问题详细信息响应，请在 `Startup.ConfigureServices` 中注册 `ProblemDetailsFactory` 的自定义实现：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-146">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a2ca0-147">可以按照[使用 ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) 部分所述的方式配置错误响应。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-147">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="a2ca0-148">使用 ApiBehaviorOptions.ClientErrorMapping</span><span class="sxs-lookup"><span data-stu-id="a2ca0-148">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="a2ca0-149">使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> 属性配置 `ProblemDetails` 响应的内容。</span><span class="sxs-lookup"><span data-stu-id="a2ca0-149">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping*> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="a2ca0-150">例如，`Startup.ConfigureServices` 中的以下代码会更新 404 响应的 `type` 属性：</span><span class="sxs-lookup"><span data-stu-id="a2ca0-150">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
