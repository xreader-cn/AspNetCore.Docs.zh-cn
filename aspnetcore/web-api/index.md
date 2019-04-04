---
title: 使用 ASP.NET Core 构建 Web API
author: scottaddie
description: 了解用于在 ASP.NET Core 中构建 Web API 的功能以及每项功能的适用场景。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/11/2019
uid: web-api/index
ms.openlocfilehash: bc8be67957a56a818a88e0496d45db1e7b7aed0e
ms.sourcegitcommit: a467828b5e4eaae291d961ffe2279a571900de23
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58142356"
---
# <a name="build-web-apis-with-aspnet-core"></a><span data-ttu-id="a870d-103">使用 ASP.NET Core 构建 Web API</span><span class="sxs-lookup"><span data-stu-id="a870d-103">Build web APIs with ASP.NET Core</span></span>

<span data-ttu-id="a870d-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="a870d-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="a870d-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a870d-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a870d-106">本文档说明如何在 ASP.NET Core 中构建 Web API 以及每项功能的最佳适用场景。</span><span class="sxs-lookup"><span data-stu-id="a870d-106">This document explains how to build a web API in ASP.NET Core and when it's most appropriate to use each feature.</span></span>

## <a name="derive-class-from-controllerbase"></a><span data-ttu-id="a870d-107">从 ControllerBase 派生类</span><span class="sxs-lookup"><span data-stu-id="a870d-107">Derive class from ControllerBase</span></span>

<span data-ttu-id="a870d-108">从控制器（旨在用作 Web API）中的 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 类继承。</span><span class="sxs-lookup"><span data-stu-id="a870d-108">Inherit from the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class in a controller that's intended to serve as a web API.</span></span> <span data-ttu-id="a870d-109">例如:</span><span class="sxs-lookup"><span data-stu-id="a870d-109">For example:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

<span data-ttu-id="a870d-110">通过 `ControllerBase` 类可使用一些属性和方法。</span><span class="sxs-lookup"><span data-stu-id="a870d-110">The `ControllerBase` class provides access to several properties and methods.</span></span> <span data-ttu-id="a870d-111">在前面的代码中，示例包括 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>。</span><span class="sxs-lookup"><span data-stu-id="a870d-111">In the preceding code, examples include <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>.</span></span> <span data-ttu-id="a870d-112">将在操作方法中调用这些方法以分别返回 HTTP 400 和 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="a870d-112">These methods are called within action methods to return HTTP 400 and 201 status codes, respectively.</span></span> <span data-ttu-id="a870d-113">将使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState>属性（还可由 `ControllerBase` 提供）处理请求模型验证。</span><span class="sxs-lookup"><span data-stu-id="a870d-113">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState> property, also provided by `ControllerBase`, is accessed to handle request model validation.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="annotation-with-apicontroller-attribute"></a><span data-ttu-id="a870d-114">使用 ApiController 属性进行注释</span><span class="sxs-lookup"><span data-stu-id="a870d-114">Annotation with ApiController attribute</span></span>

<span data-ttu-id="a870d-115">ASP.NET Core 2.1 引入了 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 特性，用于批注 Web API 控制器类。</span><span class="sxs-lookup"><span data-stu-id="a870d-115">ASP.NET Core 2.1 introduces the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute to denote a web API controller class.</span></span> <span data-ttu-id="a870d-116">例如:</span><span class="sxs-lookup"><span data-stu-id="a870d-116">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="a870d-117">为在控制器级别使用此特性，需要兼容性版本 2.1 或更高版本（通过 <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 设置）。</span><span class="sxs-lookup"><span data-stu-id="a870d-117">A compatibility version of 2.1 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the controller level.</span></span> <span data-ttu-id="a870d-118">例如，`Startup.ConfigureServices` 中突出显示的代码设置了 2.1 兼容性标志：</span><span class="sxs-lookup"><span data-stu-id="a870d-118">For example, the highlighted code in `Startup.ConfigureServices` sets the 2.1 compatibility flag:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=2)]

<span data-ttu-id="a870d-119">有关更多信息，请参见<xref:mvc/compatibility-version>。</span><span class="sxs-lookup"><span data-stu-id="a870d-119">For more information, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a870d-120">在 ASP.NET Core 2.2 或更高版本中，可将 `[ApiController]` 特性应用于程序集。</span><span class="sxs-lookup"><span data-stu-id="a870d-120">In ASP.NET Core 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="a870d-121">以这种方式进行注释，会将 web API 行为应用到程序集中的所有控制器。</span><span class="sxs-lookup"><span data-stu-id="a870d-121">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="a870d-122">请注意，无法针对单个控制器执行选择退出操作。</span><span class="sxs-lookup"><span data-stu-id="a870d-122">Beware that there's no way to opt out for individual controllers.</span></span> <span data-ttu-id="a870d-123">建议将程序集级别的特性应用于 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="a870d-123">As a recommendation, assembly-level attributes should be applied to the `Startup` class:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ApiControllerAttributeOnAssembly&highlight=1)]

<span data-ttu-id="a870d-124">为在程序集级别使用此特性，需要兼容性版本 2.2 或更高版本（通过 <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 设置）。</span><span class="sxs-lookup"><span data-stu-id="a870d-124">A compatibility version of 2.2 or later, set via <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>, is required to use this attribute at the assembly level.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a870d-125">`[ApiController]` 特性通常结合 `ControllerBase` 来为控制器启用特定于 REST 行为。</span><span class="sxs-lookup"><span data-stu-id="a870d-125">The `[ApiController]` attribute is commonly coupled with `ControllerBase` to enable REST-specific behavior for controllers.</span></span> <span data-ttu-id="a870d-126">通过 `ControllerBase` 可使用<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*> 等方法。</span><span class="sxs-lookup"><span data-stu-id="a870d-126">`ControllerBase` provides access to methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.</span></span>

<span data-ttu-id="a870d-127">另一种方法是创建使用 `[ApiController]` 特性进行批注的自定义基本控制器类：</span><span class="sxs-lookup"><span data-stu-id="a870d-127">Another approach is to create a custom base controller class annotated with the `[ApiController]` attribute:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]

<span data-ttu-id="a870d-128">以下各部分说明该特性添加的便利功能。</span><span class="sxs-lookup"><span data-stu-id="a870d-128">The following sections describe convenience features added by the attribute.</span></span>

### <a name="automatic-http-400-responses"></a><span data-ttu-id="a870d-129">自动 HTTP 400 响应</span><span class="sxs-lookup"><span data-stu-id="a870d-129">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="a870d-130">模型验证错误会自动触发 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="a870d-130">Model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="a870d-131">因此，操作中不需要以下代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-131">Consequently, the following code becomes unnecessary in your actions:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_ModelStateIsValidCheck)]

<span data-ttu-id="a870d-132">使用 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> 自定义所生成的响应的输出。</span><span class="sxs-lookup"><span data-stu-id="a870d-132">Use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to customize the output of the resulting response.</span></span>

<span data-ttu-id="a870d-133">如果操作可从模型验证错误中恢复，禁用默认行为是有用的。</span><span class="sxs-lookup"><span data-stu-id="a870d-133">Disabling the default behavior is useful when your action can recover from a model validation error.</span></span> <span data-ttu-id="a870d-134">当 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 属性设置为 `true` 时，会禁用默认行为。</span><span class="sxs-lookup"><span data-stu-id="a870d-134">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property is set to `true`.</span></span> <span data-ttu-id="a870d-135">在 `Startup.ConfigureServices` 中的 `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);` 后添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-135">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a870d-136">使用 2.2 或更高版本的兼容性标志时，HTTP 400 响应的默认响应类型为 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>。</span><span class="sxs-lookup"><span data-stu-id="a870d-136">With a compatibility flag of 2.2 or later, the default response type for HTTP 400 responses is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="a870d-137">`ValidationProblemDetails` 类型符合 [RFC 7807 规范](https://tools.ietf.org/html/rfc7807)。</span><span class="sxs-lookup"><span data-stu-id="a870d-137">The `ValidationProblemDetails` type complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span> <span data-ttu-id="a870d-138">将 `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` 属性设置为 `true` 以返回 ASP.NET Core 2.1 错误格式 <xref:Microsoft.AspNetCore.Mvc.SerializableError>。</span><span class="sxs-lookup"><span data-stu-id="a870d-138">Set the `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` property to `true` to instead return the ASP.NET Core 2.1 error format of <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="a870d-139">在 `Startup.ConfigureServices` 中添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-139">Add the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2)
    .ConfigureApiBehaviorOptions(options =>
    {
        options
          .SuppressUseValidationProblemDetailsForInvalidModelStateResponses = true;
    });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="binding-source-parameter-inference"></a><span data-ttu-id="a870d-140">绑定源参数推理</span><span class="sxs-lookup"><span data-stu-id="a870d-140">Binding source parameter inference</span></span>

<span data-ttu-id="a870d-141">绑定源特性定义可找到操作参数值的位置。</span><span class="sxs-lookup"><span data-stu-id="a870d-141">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="a870d-142">存在以下绑定源特性：</span><span class="sxs-lookup"><span data-stu-id="a870d-142">The following binding source attributes exist:</span></span>

|<span data-ttu-id="a870d-143">特性</span><span class="sxs-lookup"><span data-stu-id="a870d-143">Attribute</span></span>|<span data-ttu-id="a870d-144">绑定源</span><span class="sxs-lookup"><span data-stu-id="a870d-144">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="a870d-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span><span class="sxs-lookup"><span data-stu-id="a870d-145">**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**</span></span>     | <span data-ttu-id="a870d-146">请求正文</span><span class="sxs-lookup"><span data-stu-id="a870d-146">Request body</span></span> |
|<span data-ttu-id="a870d-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span><span class="sxs-lookup"><span data-stu-id="a870d-147">**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**</span></span>     | <span data-ttu-id="a870d-148">请求正文中的表单数据</span><span class="sxs-lookup"><span data-stu-id="a870d-148">Form data in the request body</span></span> |
|<span data-ttu-id="a870d-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span><span class="sxs-lookup"><span data-stu-id="a870d-149">**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)**</span></span> | <span data-ttu-id="a870d-150">请求标头</span><span class="sxs-lookup"><span data-stu-id="a870d-150">Request header</span></span> |
|<span data-ttu-id="a870d-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span><span class="sxs-lookup"><span data-stu-id="a870d-151">**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**</span></span>   | <span data-ttu-id="a870d-152">请求查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="a870d-152">Request query string parameter</span></span> |
|<span data-ttu-id="a870d-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span><span class="sxs-lookup"><span data-stu-id="a870d-153">**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**</span></span>   | <span data-ttu-id="a870d-154">当前请求中的路由数据</span><span class="sxs-lookup"><span data-stu-id="a870d-154">Route data from the current request</span></span> |
|<span data-ttu-id="a870d-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span><span class="sxs-lookup"><span data-stu-id="a870d-155">**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)**</span></span> | <span data-ttu-id="a870d-156">作为操作参数插入的请求服务</span><span class="sxs-lookup"><span data-stu-id="a870d-156">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="a870d-157">当值可能包含 `%2f`（即 `/`）时，请勿使用 `[FromRoute]`。</span><span class="sxs-lookup"><span data-stu-id="a870d-157">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="a870d-158">`%2f` 不会转换为 `/`（非转移形式）。</span><span class="sxs-lookup"><span data-stu-id="a870d-158">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="a870d-159">如果值可能包含 `%2f`，则使用 `[FromQuery]`。</span><span class="sxs-lookup"><span data-stu-id="a870d-159">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="a870d-160">没有 `[ApiController]` 特性时，将显式定义绑定源特性。</span><span class="sxs-lookup"><span data-stu-id="a870d-160">Without the `[ApiController]` attribute, binding source attributes are explicitly defined.</span></span> <span data-ttu-id="a870d-161">如果没有 `[ApiController]` 或其他绑定源属性（如 `[FromQuery]`），ASP.NET Core 运行时会尝试使用复杂对象模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="a870d-161">Without `[ApiController]` or other binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="a870d-162">复杂对象模型绑定器从值提供程序（包含已定义的顺序）拉取数据。</span><span class="sxs-lookup"><span data-stu-id="a870d-162">The complex object model binder pulls data from value providers (which have a defined order).</span></span> <span data-ttu-id="a870d-163">例如，始终选择启用“body 模型绑定器”。</span><span class="sxs-lookup"><span data-stu-id="a870d-163">For instance, the 'body model binder' is always opt in.</span></span>

<span data-ttu-id="a870d-164">在下面的示例中，`[FromQuery]` 特性指示 `discontinuedOnly` 参数值在请求 URL 的查询字符串中提供：</span><span class="sxs-lookup"><span data-stu-id="a870d-164">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="a870d-165">推理规则应用于操作参数的默认数据源。</span><span class="sxs-lookup"><span data-stu-id="a870d-165">Inference rules are applied for the default data sources of action parameters.</span></span> <span data-ttu-id="a870d-166">这些规则将配置绑定资源，否则你可以手动应用操作参数。</span><span class="sxs-lookup"><span data-stu-id="a870d-166">These rules configure the binding sources you're otherwise likely to manually apply to the action parameters.</span></span> <span data-ttu-id="a870d-167">绑定源特性的行为如下：</span><span class="sxs-lookup"><span data-stu-id="a870d-167">The binding source attributes behave as follows:</span></span>

* <span data-ttu-id="a870d-168">**[FromBody]**，针对复杂类型参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="a870d-168">**[FromBody]** is inferred for complex type parameters.</span></span> <span data-ttu-id="a870d-169">此规则不适用于具有特殊含义的任何复杂的内置类型，如 <xref:Microsoft.AspNetCore.Http.IFormCollection> 和 <xref:System.Threading.CancellationToken>。</span><span class="sxs-lookup"><span data-stu-id="a870d-169">An exception to this rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="a870d-170">绑定源推理代码将忽略这些特殊类型。</span><span class="sxs-lookup"><span data-stu-id="a870d-170">The binding source inference code ignores those special types.</span></span> <span data-ttu-id="a870d-171">对于简单类型（例如 `string` 或 `int`），推断不出 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="a870d-171">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="a870d-172">因此，如果需要该功能，对于简单类型，应使用 `[FromBody]` 属性。</span><span class="sxs-lookup"><span data-stu-id="a870d-172">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span> <span data-ttu-id="a870d-173">当操作中的多个参数为显式指定（通过 `[FromBody]`）或在请求正文中作为绑定进行推断时，将会引发异常。</span><span class="sxs-lookup"><span data-stu-id="a870d-173">When an action has more than one parameter explicitly specified (via `[FromBody]`) or inferred as bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="a870d-174">例如，下面的操作签名会导致异常：</span><span class="sxs-lookup"><span data-stu-id="a870d-174">For example, the following action signatures cause an exception:</span></span>

    [!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]

    > [!NOTE]
    > <span data-ttu-id="a870d-175">在 ASP.NET Core 2.1 中，集合类型参数（如列表和数组）被不正确地推断为 [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)。</span><span class="sxs-lookup"><span data-stu-id="a870d-175">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute).</span></span> <span data-ttu-id="a870d-176">若要从请求正文中绑定参数，应对这些参数使用 [[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)。</span><span class="sxs-lookup"><span data-stu-id="a870d-176">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="a870d-177">此行为在 ASP.NET Core 2.2 或更高版本中得到了修复，其中集合类型参数默认被推断为从正文中绑定。</span><span class="sxs-lookup"><span data-stu-id="a870d-177">This behavior is fixed in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

* <span data-ttu-id="a870d-178">[FromForm] 是针对 <xref:Microsoft.AspNetCore.Http.IFormFile> 和 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 类型的操作参数推断出来的。</span><span class="sxs-lookup"><span data-stu-id="a870d-178">**[FromForm]** is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="a870d-179">该特性不针对任何简单类型或用户定义类型进行推断。</span><span class="sxs-lookup"><span data-stu-id="a870d-179">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="a870d-180">**[FromRoute]**，针对与路由模板中的参数相匹配的任何操作参数名称进行推断。</span><span class="sxs-lookup"><span data-stu-id="a870d-180">**[FromRoute]** is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="a870d-181">当多个路由与一个操作参数匹配时，任何路由值都视为 `[FromRoute]`。</span><span class="sxs-lookup"><span data-stu-id="a870d-181">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="a870d-182">**[FromQuery]**，针对任何其他操作参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="a870d-182">**[FromQuery]** is inferred for any other action parameters.</span></span>

<span data-ttu-id="a870d-183">当 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> 属性设置为 `true` 时，会禁用默认推理规则。</span><span class="sxs-lookup"><span data-stu-id="a870d-183">The default inference rules are disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> property is set to `true`.</span></span> <span data-ttu-id="a870d-184">在 `Startup.ConfigureServices` 中的 `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);` 后添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-184">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_<version_number>);`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="multipartform-data-request-inference"></a><span data-ttu-id="a870d-185">Multipart/form-data 请求推理</span><span class="sxs-lookup"><span data-stu-id="a870d-185">Multipart/form-data request inference</span></span>

<span data-ttu-id="a870d-186">使用 [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 特性批注操作参数时，将推断 `multipart/form-data` 请求内容类型。</span><span class="sxs-lookup"><span data-stu-id="a870d-186">When an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute, the `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="a870d-187">当 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> 属性设置为 `true` 时，会禁用默认行为。</span><span class="sxs-lookup"><span data-stu-id="a870d-187">The default behavior is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property is set to `true`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a870d-188">在 `Startup.ConfigureServices` 中添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-188">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="a870d-189">在 `Startup.ConfigureServices` 中的 `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);` 后添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-189">Add the following code in `Startup.ConfigureServices` after `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="attribute-routing-requirement"></a><span data-ttu-id="a870d-190">特性路由要求</span><span class="sxs-lookup"><span data-stu-id="a870d-190">Attribute routing requirement</span></span>

<span data-ttu-id="a870d-191">特性路由是必要条件。</span><span class="sxs-lookup"><span data-stu-id="a870d-191">Attribute routing becomes a requirement.</span></span> <span data-ttu-id="a870d-192">例如:</span><span class="sxs-lookup"><span data-stu-id="a870d-192">For example:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="a870d-193">不能通过 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 中定义的[传统路由](xref:mvc/controllers/routing#conventional-routing)或通过 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 访问操作。</span><span class="sxs-lookup"><span data-stu-id="a870d-193">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined in <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="problem-details-responses-for-error-status-codes"></a><span data-ttu-id="a870d-194">错误状态代码的问题详细信息响应</span><span class="sxs-lookup"><span data-stu-id="a870d-194">Problem details responses for error status codes</span></span>

<span data-ttu-id="a870d-195">在 ASP.NET Core 2.2 或更高版本中，MVC 会将错误结果（状态代码为 400 或更高的结果）转换为状态代码为 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 的结果。</span><span class="sxs-lookup"><span data-stu-id="a870d-195">In ASP.NET Core 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="a870d-196">`ProblemDetails` 为：</span><span class="sxs-lookup"><span data-stu-id="a870d-196">`ProblemDetails` is:</span></span>

* <span data-ttu-id="a870d-197">基于 [RFC 7807 规范](https://tools.ietf.org/html/rfc7807)的类型。</span><span class="sxs-lookup"><span data-stu-id="a870d-197">A type based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>
* <span data-ttu-id="a870d-198">用于指定 HTTP 响应中计算机可读的错误详细信息的标准化格式。</span><span class="sxs-lookup"><span data-stu-id="a870d-198">A standardized format for specifying machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="a870d-199">考虑在控制器操作中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-199">Consider the following code in a controller action:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Controllers/ProductsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="a870d-200">`NotFound` 的 HTTP 响应具有 404 状态代码和 `ProblemDetails` 正文。</span><span class="sxs-lookup"><span data-stu-id="a870d-200">The HTTP response for `NotFound` has a 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="a870d-201">例如:</span><span class="sxs-lookup"><span data-stu-id="a870d-201">For example:</span></span>

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

<span data-ttu-id="a870d-202">需具备 2.2 版本或更高版本的兼容性标志，才可使用问题详细信息功能。</span><span class="sxs-lookup"><span data-stu-id="a870d-202">The problem details feature requires a compatibility flag of 2.2 or later.</span></span> <span data-ttu-id="a870d-203">当 `SuppressMapClientErrors` 属性设置为 `true` 时，会禁用默认行为。</span><span class="sxs-lookup"><span data-stu-id="a870d-203">The default behavior is disabled when the `SuppressMapClientErrors` property is set to `true`.</span></span> <span data-ttu-id="a870d-204">在 `Startup.ConfigureServices` 中添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="a870d-204">Add the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8)]

<span data-ttu-id="a870d-205">使用 `ClientErrorMapping` 属性配置 `ProblemDetails` 响应的内容。</span><span class="sxs-lookup"><span data-stu-id="a870d-205">Use the `ClientErrorMapping` property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="a870d-206">例如，以下代码会更新 404 响应的 `type` 属性：</span><span class="sxs-lookup"><span data-stu-id="a870d-206">For example, the following code updates the `type` property for 404 responses:</span></span>

[!code-csharp[](define-controller/samples/WebApiSample.Api.22/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a870d-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="a870d-207">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
