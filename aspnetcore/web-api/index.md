---
title: 使用 ASP.NET Core 创建 Web API
author: scottaddie
description: 了解在 ASP.NET Core 中创建 Web API 的基础知识。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2019
uid: web-api/index
ms.openlocfilehash: aab9b848eb6e69055b019c9253c716898e9847e2
ms.sourcegitcommit: a11f09c10ef3d4eeab7ae9ce993e7f30427741c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149342"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="f9e4f-103">使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="f9e4f-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="f9e4f-104">作者：[Scott Addie](https://github.com/scottaddie) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f9e4f-105">ASP.NET Core 支持使用 C# 创建 RESTful 服务，也称为 Web API。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="f9e4f-106">若要处理请求，Web API 使用控制器。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="f9e4f-107">Web API 中的*控制器*是派生自 `ControllerBase` 的类。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="f9e4f-108">本文介绍了如何使用控制器处理 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="f9e4f-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="f9e4f-110">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="f9e4f-111">ControllerBase 类</span><span class="sxs-lookup"><span data-stu-id="f9e4f-111">ControllerBase class</span></span>

<span data-ttu-id="f9e4f-112">Web API 包含一个或多个派生自 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 的控制器类。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="f9e4f-113">Web API 项目模板提供了一个入门版控制器：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="f9e4f-114">不要通过从 <xref:Microsoft.AspNetCore.Mvc.Controller> 类派生来创建 Web API 控制器。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="f9e4f-115">`Controller` 派生自 `ControllerBase`，并添加对视图的支持，因此它用于处理 Web 页面，而不是 Web API 请求。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="f9e4f-116">此规则有一个例外：如果打算为视图和 Web API 使用相同的控制器，则从 `Controller` 派生控制器。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="f9e4f-117">`ControllerBase` 类提供了很多用于处理 HTTP 请求的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="f9e4f-118">例如，`ControllerBase.CreatedAtAction` 返回 201 状态代码：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="f9e4f-119">下面是 `ControllerBase` 提供的方法的更多示例。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="f9e4f-120">方法</span><span class="sxs-lookup"><span data-stu-id="f9e4f-120">Method</span></span>   |<span data-ttu-id="f9e4f-121">说明</span><span class="sxs-lookup"><span data-stu-id="f9e4f-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| <span data-ttu-id="f9e4f-122">返回 400 状态代码。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>|<span data-ttu-id="f9e4f-123">返回 404 状态代码。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|<span data-ttu-id="f9e4f-124">返回文件。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|<span data-ttu-id="f9e4f-125">调用[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|<span data-ttu-id="f9e4f-126">调用[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="f9e4f-127">有关可用方法和属性的列表，请参阅 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="f9e4f-128">特性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-128">Attributes</span></span>

<span data-ttu-id="f9e4f-129"><xref:Microsoft.AspNetCore.Mvc> 命名空间提供可用于配置 Web API 控制器的行为和操作方法的属性。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="f9e4f-130">下述示例使用属性来指定受支持的 HTTP 操作谓词和所有可返回的已知 HTTP 状态代码：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="f9e4f-131">以下是可用属性的更多示例。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="f9e4f-132">特性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-132">Attribute</span></span>|<span data-ttu-id="f9e4f-133">说明</span><span class="sxs-lookup"><span data-stu-id="f9e4f-133">Notes</span></span>|
|---------|-----|
|<span data-ttu-id="f9e4f-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-134">[[Route]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)</span></span>      |<span data-ttu-id="f9e4f-135">指定控制器或操作的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-135">Specifies URL pattern for a controller or action.</span></span>|
|<span data-ttu-id="f9e4f-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-136">[[Bind]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)</span></span>        |<span data-ttu-id="f9e4f-137">指定要包含的前缀和属性，以进行模型绑定。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-137">Specifies prefix and properties to include for model binding.</span></span>|
|<span data-ttu-id="f9e4f-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-138">[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)</span></span>  |<span data-ttu-id="f9e4f-139">标识支持 HTTP GET 操作谓词的操作。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-139">Identifies an action that supports the HTTP GET action verb.</span></span>|
|<span data-ttu-id="f9e4f-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-140">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)</span></span>|<span data-ttu-id="f9e4f-141">指定某个操作接受的数据类型。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-141">Specifies data types that an action accepts.</span></span>|
|<span data-ttu-id="f9e4f-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-142">[[Produces]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)</span></span>|<span data-ttu-id="f9e4f-143">指定某个操作返回的数据类型。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-143">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="f9e4f-144">有关包含可用属性的列表，请参阅 <xref:Microsoft.AspNetCore.Mvc> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-144">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="f9e4f-145">ApiController 属性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-145">ApiController attribute</span></span>

<span data-ttu-id="f9e4f-146">[[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性可应用于控制器类，以启用下述 API 特定的固定行为：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-146">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

* [<span data-ttu-id="f9e4f-147">属性路由要求</span><span class="sxs-lookup"><span data-stu-id="f9e4f-147">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="f9e4f-148">自动 HTTP 400 响应</span><span class="sxs-lookup"><span data-stu-id="f9e4f-148">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="f9e4f-149">绑定源参数推理</span><span class="sxs-lookup"><span data-stu-id="f9e4f-149">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="f9e4f-150">Multipart/form-data 请求推理</span><span class="sxs-lookup"><span data-stu-id="f9e4f-150">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="f9e4f-151">错误状态代码的问题详细信息</span><span class="sxs-lookup"><span data-stu-id="f9e4f-151">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="f9e4f-152">这些功能需要[兼容性版本](xref:mvc/compatibility-version)为 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-152">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="f9e4f-153">特定控制器上的属性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-153">Attribute on specific controllers</span></span>

<span data-ttu-id="f9e4f-154">`[ApiController]` 属性可应用于特定控制器，如项目模板中的以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-154">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="f9e4f-155">多个控制器上的属性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-155">Attribute on multiple controllers</span></span>

<span data-ttu-id="f9e4f-156">在多个控制器上使用该属性的一种方法是创建通过 `[ApiController]` 属性批注的自定义基控制器类。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-156">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="f9e4f-157">下述示例展示了自定义基类以及从其派生的控制器：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-157">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="f9e4f-158">程序集上的属性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-158">Attribute on an assembly</span></span>

<span data-ttu-id="f9e4f-159">如果将[兼容性版本](xref:mvc/compatibility-version)设置为 2.2 或更高版本，则 `[ApiController]` 属性可应用于程序集。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-159">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="f9e4f-160">以这种方式进行注释，会将 web API 行为应用到程序集中的所有控制器。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-160">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="f9e4f-161">无法针对单个控制器执行选择退出操作。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-161">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="f9e4f-162">将程序集级别的属性应用于 `Startup` 类两侧的命名空间声明：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-162">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="f9e4f-163">特性路由要求</span><span class="sxs-lookup"><span data-stu-id="f9e4f-163">Attribute routing requirement</span></span>

<span data-ttu-id="f9e4f-164">`[ApiController]` 属性使属性路由成为要求。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-164">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="f9e4f-165">例如:</span><span class="sxs-lookup"><span data-stu-id="f9e4f-165">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="f9e4f-166">不能通过由 `Startup.Configure` 中的 `UseEndpoints`、<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 或 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 定义的[传统路由](xref:mvc/controllers/routing#conventional-routing)访问操作。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-166">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="f9e4f-167">不能通过 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> 定义的[传统路由](xref:mvc/controllers/routing#conventional-routing)或通过 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 访问操作。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> in `Startup.Configure`.</span></span>

::: moniker-end

### <a name="automatic-http-400-responses"></a><span data-ttu-id="f9e4f-168">自动 HTTP 400 响应</span><span class="sxs-lookup"><span data-stu-id="f9e4f-168">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="f9e4f-169">`[ApiController]` 属性使模型验证错误自动触发 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-169">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="f9e4f-170">因此，操作方法中不需要以下代码：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-170">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="f9e4f-171">ASP.NET Core MVC 使用 <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> 操作筛选器来执行上述检查。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-171">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="f9e4f-172">默认 BadRequest 响应</span><span class="sxs-lookup"><span data-stu-id="f9e4f-172">Default BadRequest response</span></span>

<span data-ttu-id="f9e4f-173">使用 2.1 的兼容性版本时，HTTP 400 响应的默认响应类型为 <xref:Microsoft.AspNetCore.Mvc.SerializableError>。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-173">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="f9e4f-174">下述请求正文是序列化类型的示例：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-174">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f9e4f-175">使用 2.2 或更高版本的兼容性版本时，HTTP 400 响应的默认响应类型为 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-175">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="f9e4f-176">下述请求正文是序列化类型的示例：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-176">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="f9e4f-177">`ValidationProblemDetails` 类型：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-177">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="f9e4f-178">提供计算机可读的格式来指定 Web API 响应中的错误。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-178">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="f9e4f-179">符合 [RFC 7807 规范](https://tools.ietf.org/html/rfc7807)。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-179">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="f9e4f-180">记录自动 400 响应</span><span class="sxs-lookup"><span data-stu-id="f9e4f-180">Log automatic 400 responses</span></span>

<span data-ttu-id="f9e4f-181">请参阅[如何对模型验证错误记录自动 400 响应 (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157)。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-181">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="f9e4f-182">禁用自动 400 响应</span><span class="sxs-lookup"><span data-stu-id="f9e4f-182">Disable automatic 400 response</span></span>

<span data-ttu-id="f9e4f-183">若要禁用自动 400 行为，请将 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-183">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="f9e4f-184">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-184">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="f9e4f-185">绑定源参数推理</span><span class="sxs-lookup"><span data-stu-id="f9e4f-185">Binding source parameter inference</span></span>

<span data-ttu-id="f9e4f-186">绑定源特性定义可找到操作参数值的位置。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-186">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="f9e4f-187">存在以下绑定源特性：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-187">The following binding source attributes exist:</span></span>

|<span data-ttu-id="f9e4f-188">特性</span><span class="sxs-lookup"><span data-stu-id="f9e4f-188">Attribute</span></span>|<span data-ttu-id="f9e4f-189">绑定源</span><span class="sxs-lookup"><span data-stu-id="f9e4f-189">Binding source</span></span> |
|---------|---------|
|<span data-ttu-id="f9e4f-190">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-190">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)</span></span>     | <span data-ttu-id="f9e4f-191">请求正文</span><span class="sxs-lookup"><span data-stu-id="f9e4f-191">Request body</span></span> |
|<span data-ttu-id="f9e4f-192">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-192">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)</span></span>     | <span data-ttu-id="f9e4f-193">请求正文中的表单数据</span><span class="sxs-lookup"><span data-stu-id="f9e4f-193">Form data in the request body</span></span> |
|<span data-ttu-id="f9e4f-194">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-194">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)</span></span> | <span data-ttu-id="f9e4f-195">请求标头</span><span class="sxs-lookup"><span data-stu-id="f9e4f-195">Request header</span></span> |
|<span data-ttu-id="f9e4f-196">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-196">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)</span></span>   | <span data-ttu-id="f9e4f-197">请求查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="f9e4f-197">Request query string parameter</span></span> |
|<span data-ttu-id="f9e4f-198">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-198">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)</span></span>   | <span data-ttu-id="f9e4f-199">当前请求中的路由数据</span><span class="sxs-lookup"><span data-stu-id="f9e4f-199">Route data from the current request</span></span> |
|<span data-ttu-id="f9e4f-200">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span><span class="sxs-lookup"><span data-stu-id="f9e4f-200">[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)</span></span> | <span data-ttu-id="f9e4f-201">作为操作参数插入的请求服务</span><span class="sxs-lookup"><span data-stu-id="f9e4f-201">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="f9e4f-202">当值可能包含 `%2f`（即 `/`）时，请勿使用 `[FromRoute]`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-202">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="f9e4f-203">`%2f` 不会转换为 `/`（非转移形式）。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-203">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="f9e4f-204">如果值可能包含 `%2f`，则使用 `[FromQuery]`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-204">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="f9e4f-205">如果没有 `[ApiController]` 属性或诸如 `[FromQuery]` 的绑定源属性，ASP.NET Core 运行时会尝试使用复杂对象模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-205">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="f9e4f-206">复杂对象模型绑定器按已定义顺序从值提供程序拉取数据。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-206">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="f9e4f-207">在下面的示例中，`[FromQuery]` 特性指示 `discontinuedOnly` 参数值在请求 URL 的查询字符串中提供：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-207">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="f9e4f-208">`[ApiController]` 属性将推理规则应用于操作参数的默认数据源。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-208">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="f9e4f-209">借助这些规则，无需通过将属性应用于操作参数来手动识别绑定源。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-209">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="f9e4f-210">绑定源推理规则的行为如下：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-210">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="f9e4f-211">`[FromBody]` 针对复杂类型参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-211">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="f9e4f-212">`[FromBody]` 不适用于具有特殊含义的任何复杂的内置类型，如 <xref:Microsoft.AspNetCore.Http.IFormCollection> 和 <xref:System.Threading.CancellationToken>。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-212">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="f9e4f-213">绑定源推理代码将忽略这些特殊类型。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-213">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="f9e4f-214">`[FromForm]` 针对 <xref:Microsoft.AspNetCore.Http.IFormFile> 和 <xref:Microsoft.AspNetCore.Http.IFormFileCollection> 类型的操作参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-214">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="f9e4f-215">该特性不针对任何简单类型或用户定义类型进行推断。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-215">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="f9e4f-216">`[FromRoute]` 针对与路由模板中的参数相匹配的任何操作参数名称进行推断。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-216">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="f9e4f-217">当多个路由与一个操作参数匹配时，任何路由值都视为 `[FromRoute]`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-217">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="f9e4f-218">`[FromQuery]` 针对任何其他操作参数进行推断。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-218">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="f9e4f-219">FromBody 推理说明</span><span class="sxs-lookup"><span data-stu-id="f9e4f-219">FromBody inference notes</span></span>

<span data-ttu-id="f9e4f-220">对于简单类型（例如 `string` 或 `int`），推断不出 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-220">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="f9e4f-221">因此，如果需要该功能，对于简单类型，应使用 `[FromBody]` 属性。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-221">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="f9e4f-222">当操作拥有多个从请求正文中绑定的参数时，将会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-222">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="f9e4f-223">例如，以下所有操作方法签名都会导致异常：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-223">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="f9e4f-224">`[FromBody]` 对两者进行推断，因为它们是复杂类型。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-224">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="f9e4f-225">`[FromBody]` 对一个进行归属，对另一个进行推断，因为它是复杂类型。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-225">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="f9e4f-226">`[FromBody]` 对两者进行归属。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-226">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="f9e4f-227">在 ASP.NET Core 2.1 中，集合类型参数（如列表和数组）被不正确地推断为 `[FromQuery]`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-227">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="f9e4f-228">若要从请求正文中绑定参数，应对这些参数使用 `[FromBody]` 属性。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-228">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="f9e4f-229">此行为在 ASP.NET Core 2.2 或更高版本中得到了更正，其中集合类型参数默认被推断为从正文中绑定。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-229">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="f9e4f-230">禁用推理规则</span><span class="sxs-lookup"><span data-stu-id="f9e4f-230">Disable inference rules</span></span>

<span data-ttu-id="f9e4f-231">若要禁用绑定源推理，请将 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-231">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="f9e4f-232">在 `Startup.ConfigureServices` 中添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-232">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="f9e4f-233">Multipart/form-data 请求推理</span><span class="sxs-lookup"><span data-stu-id="f9e4f-233">Multipart/form-data request inference</span></span>

<span data-ttu-id="f9e4f-234">使用 [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) 属性批注操作参数时，`[ApiController]` 属性应用推理规则。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-234">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="f9e4f-235">将推断 `multipart/form-data` 请求内容类型。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-235">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="f9e4f-236">要禁用默认行为，请在 `Startup.ConfigureServices` 中将 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-236">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="f9e4f-237">错误状态代码的问题详细信息</span><span class="sxs-lookup"><span data-stu-id="f9e4f-237">Problem details for error status codes</span></span>

<span data-ttu-id="f9e4f-238">当兼容性版本为 2.2 或更高版本时，MVC 会将错误结果（状态代码为 400 或更高的结果）转换为状态代码为 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 的结果。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-238">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="f9e4f-239">`ProblemDetails` 类型基于 [RFC 7807 规范](https://tools.ietf.org/html/rfc7807)，用于提供 HTTP 响应中计算机可读的错误详细信息。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-239">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="f9e4f-240">考虑在控制器操作中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-240">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="f9e4f-241">`NotFound` 方法会生成带 `ProblemDetails` 正文的 HTTP 404 状态代码。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-241">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="f9e4f-242">例如:</span><span class="sxs-lookup"><span data-stu-id="f9e4f-242">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="f9e4f-243">禁用 ProblemDetails 响应</span><span class="sxs-lookup"><span data-stu-id="f9e4f-243">Disable ProblemDetails response</span></span>

<span data-ttu-id="f9e4f-244">当 <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> 属性设置为 `true` 时，会禁止自动创建 `ProblemDetails` 实例。</span><span class="sxs-lookup"><span data-stu-id="f9e4f-244">The automatic creation of a `ProblemDetails` instance is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> property is set to `true`.</span></span> <span data-ttu-id="f9e4f-245">在 `Startup.ConfigureServices` 中添加下列代码：</span><span class="sxs-lookup"><span data-stu-id="f9e4f-245">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f9e4f-246">其他资源</span><span class="sxs-lookup"><span data-stu-id="f9e4f-246">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
