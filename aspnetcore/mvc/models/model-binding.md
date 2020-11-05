---
title: ASP.NET Core 中的模型绑定
author: rick-anderson
description: 了解 ASP.NET Core 中模型绑定的工作原理以及如何自定义模型绑定的行为。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/models/model-binding
ms.openlocfilehash: 49300d32096e577db9b13a0510cc310b91ddb51d
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365348"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="392cf-103">ASP.NET Core 中的模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="392cf-104">本文解释了模型绑定的定义、模型绑定的工作原理，以及如何自定义模型绑定的行为。</span><span class="sxs-lookup"><span data-stu-id="392cf-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="392cf-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="392cf-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="392cf-106">什么是模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-106">What is Model binding</span></span>

<span data-ttu-id="392cf-107">控制器和 :::no-loc(Razor)::: 页面处理来自 HTTP 请求的数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-107">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="392cf-108">例如，路由数据可以提供一个记录键，而发布的表单域可以为模型的属性提供一个值。</span><span class="sxs-lookup"><span data-stu-id="392cf-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="392cf-109">编写代码以检索这些值，并将其从字符串转换为 .NET 类型不仅繁琐，而且还容易出错。</span><span class="sxs-lookup"><span data-stu-id="392cf-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="392cf-110">模型绑定会自动化该过程。</span><span class="sxs-lookup"><span data-stu-id="392cf-110">Model binding automates this process.</span></span> <span data-ttu-id="392cf-111">模型绑定系统：</span><span class="sxs-lookup"><span data-stu-id="392cf-111">The model binding system:</span></span>

* <span data-ttu-id="392cf-112">从各种源（如路由数据、表单域和查询字符串）中检索数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="392cf-113">:::no-loc(Razor):::在方法参数和公共属性中向控制器和页面提供数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-113">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="392cf-114">将字符串数据转换为 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="392cf-115">更新复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="392cf-116">示例</span><span class="sxs-lookup"><span data-stu-id="392cf-116">Example</span></span>

<span data-ttu-id="392cf-117">假设有以下操作方法：</span><span class="sxs-lookup"><span data-stu-id="392cf-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="392cf-118">并且应用收到一个带有以下 URL 的请求：</span><span class="sxs-lookup"><span data-stu-id="392cf-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="392cf-119">在路由系统选择该操作方法之后，模型绑定执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="392cf-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="392cf-120">查找 `GetByID` 的第一个参数，该参数是一个名为 `id` 的整数。</span><span class="sxs-lookup"><span data-stu-id="392cf-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="392cf-121">查找 HTTP 请求中的可用源，并在路由数据中查找 `id` =“2”。</span><span class="sxs-lookup"><span data-stu-id="392cf-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="392cf-122">将字符串“2”转换为整数 2。</span><span class="sxs-lookup"><span data-stu-id="392cf-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="392cf-123">查找 `GetByID` 的下一个参数，该参数是一个名为 `dogsOnly` 的布尔值。</span><span class="sxs-lookup"><span data-stu-id="392cf-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="392cf-124">查找源，并在查询字符串中查找“DogsOnly=true”。</span><span class="sxs-lookup"><span data-stu-id="392cf-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="392cf-125">名称匹配不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="392cf-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="392cf-126">将字符串“true”转换为布尔值 `true`。</span><span class="sxs-lookup"><span data-stu-id="392cf-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="392cf-127">然后，该框架会调用 `GetById` 方法，为 `id` 参数传入 2，并为 `dogsOnly` 参数传入 `true`。</span><span class="sxs-lookup"><span data-stu-id="392cf-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="392cf-128">在前面的示例中，模型绑定目标是简单类型的方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="392cf-129">目标也可以是复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="392cf-130">成功绑定每个属性后，将对属性进行[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="392cf-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="392cf-131">有关绑定到模型的数据以及任意绑定或验证错误的记录都存储在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 中。</span><span class="sxs-lookup"><span data-stu-id="392cf-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="392cf-132">为查明该过程是否已成功，应用会检查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 标志。</span><span class="sxs-lookup"><span data-stu-id="392cf-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="392cf-133">目标</span><span class="sxs-lookup"><span data-stu-id="392cf-133">Targets</span></span>

<span data-ttu-id="392cf-134">模型绑定尝试查找以下类型目标的值：</span><span class="sxs-lookup"><span data-stu-id="392cf-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="392cf-135">将请求路由到的控制器操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="392cf-136">:::no-loc(Razor):::请求路由到的页处理程序方法的参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-136">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="392cf-137">控制器或 `PageModel` 类的公共属性（若由特性指定）。</span><span class="sxs-lookup"><span data-stu-id="392cf-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="392cf-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-138">[BindProperty] attribute</span></span>

<span data-ttu-id="392cf-139">可应用于控制器或 `PageModel` 类的公共属性，从而使模型绑定以该属性为目标：</span><span class="sxs-lookup"><span data-stu-id="392cf-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="392cf-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-140">[BindProperties] attribute</span></span>

<span data-ttu-id="392cf-141">可在 ASP.NET Core 2.1 及更高版本中获得。</span><span class="sxs-lookup"><span data-stu-id="392cf-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="392cf-142">可应用于控制器或 `PageModel` 类，以使模型绑定以该类的所有公共属性为目标：</span><span class="sxs-lookup"><span data-stu-id="392cf-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="392cf-143">HTTP GET 请求的模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="392cf-144">默认情况下，不绑定 HTTP GET 请求的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="392cf-145">通常，GET 请求只需一个记录 ID 参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="392cf-146">记录 ID 用于查找数据库中的项。</span><span class="sxs-lookup"><span data-stu-id="392cf-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="392cf-147">因此，无需绑定包含模型实例的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="392cf-148">在需要将属性绑定到 GET 请求中的数据的情况下，请将 `SupportsGet` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="392cf-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="392cf-149">源</span><span class="sxs-lookup"><span data-stu-id="392cf-149">Sources</span></span>

<span data-ttu-id="392cf-150">默认情况下，模型绑定以键值对的形式从 HTTP 请求中的以下源中获取数据：</span><span class="sxs-lookup"><span data-stu-id="392cf-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="392cf-151">表单域</span><span class="sxs-lookup"><span data-stu-id="392cf-151">Form fields</span></span>
1. <span data-ttu-id="392cf-152">请求正文（对于[具有 [ApiController] 属性的控制器](xref:web-api/index#binding-source-parameter-inference)。）</span><span class="sxs-lookup"><span data-stu-id="392cf-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="392cf-153">路由数据</span><span class="sxs-lookup"><span data-stu-id="392cf-153">Route data</span></span>
1. <span data-ttu-id="392cf-154">查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="392cf-154">Query string parameters</span></span>
1. <span data-ttu-id="392cf-155">上传的文件</span><span class="sxs-lookup"><span data-stu-id="392cf-155">Uploaded files</span></span>

<span data-ttu-id="392cf-156">对于每个目标参数或属性，按照之前列表中指示的顺序扫描源。</span><span class="sxs-lookup"><span data-stu-id="392cf-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="392cf-157">有几个例外情况：</span><span class="sxs-lookup"><span data-stu-id="392cf-157">There are a few exceptions:</span></span>

* <span data-ttu-id="392cf-158">路由数据和查询字符串值仅用于简单类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="392cf-159">上传的文件仅绑定到实现 `IFormFile` 或 `IEnumerable<IFormFile>` 的目标类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="392cf-160">如果默认源不正确，请使用下列属性之一来指定源：</span><span class="sxs-lookup"><span data-stu-id="392cf-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="392cf-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -从查询字符串获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="392cf-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -从路由数据中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="392cf-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -从已发布的表单字段中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="392cf-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -从请求正文中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="392cf-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -从 HTTP 标头中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="392cf-166">这些属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-166">These attributes:</span></span>

* <span data-ttu-id="392cf-167">分别添加到模型属性（而不是模型类），如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="392cf-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="392cf-168">选择性地在构造函数中接受模型名称值。</span><span class="sxs-lookup"><span data-stu-id="392cf-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="392cf-169">提供此选项的目的是应对属性名称与请求中的值不匹配的情况。</span><span class="sxs-lookup"><span data-stu-id="392cf-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="392cf-170">例如，请求中的值可能是名称中带有连字符的标头，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="392cf-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="392cf-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-171">[FromBody] attribute</span></span>

<span data-ttu-id="392cf-172">将 `[FromBody]` 特性应用于一个参数，以便从一个 HTTP 请求的正文填充其属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="392cf-173">ASP.NET Core 运行时将读取正文的责任委托给输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="392cf-174">输入格式化程序的解释位于[本文后面部分](#input-formatters)。</span><span class="sxs-lookup"><span data-stu-id="392cf-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="392cf-175">将 `[FromBody]` 应用于复杂类型参数时，应用于其属性的任何绑定源属性都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="392cf-176">例如，以下 `Create` 操作指定从正文填充其 `pet` 参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="392cf-177">`Pet` 类指定从查询字符串参数填充其 `Breed` 属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="392cf-178">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="392cf-178">In the preceding example:</span></span>

* <span data-ttu-id="392cf-179">`[FromQuery]` 特性被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="392cf-180">`Breed` 属性未从查询字符串参数进行填充。</span><span class="sxs-lookup"><span data-stu-id="392cf-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="392cf-181">输入格式化程序只读取正文，不了解绑定源特性。</span><span class="sxs-lookup"><span data-stu-id="392cf-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="392cf-182">如果在正文中找到合适的值，则使用该值填充 `Breed` 属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="392cf-183">不要将 `[FromBody]` 应用于每个操作方法的多个参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="392cf-184">输入格式化程序读取请求流后，无法再次读取该流以绑定其他 `[FromBody]` 参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="392cf-185">其他源</span><span class="sxs-lookup"><span data-stu-id="392cf-185">Additional sources</span></span>

<span data-ttu-id="392cf-186">源数据由“值提供程序”提供给模型绑定系统。</span><span class="sxs-lookup"><span data-stu-id="392cf-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="392cf-187">你可以编写并注册自定义值提供程序，这些提供程序从其他源中获取用于模型绑定的数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="392cf-188">例如，你可能需要来自 :::no-loc(cookie)::: 或会话状态的数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-188">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="392cf-189">要从新的源中获取数据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="392cf-189">To get data from a new source:</span></span>

* <span data-ttu-id="392cf-190">创建用于实现 `IValueProvider` 的类。</span><span class="sxs-lookup"><span data-stu-id="392cf-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="392cf-191">创建用于实现 `IValueProviderFactory` 的类。</span><span class="sxs-lookup"><span data-stu-id="392cf-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="392cf-192">在 `Startup.ConfigureServices` 中注册工厂类。</span><span class="sxs-lookup"><span data-stu-id="392cf-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="392cf-193">该示例应用包含一个 [值提供程序](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) 和一个 [工厂](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) 示例，可从 s 中获取值 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="392cf-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="392cf-194">以下是 `Startup.ConfigureServices` 中的注册代码：</span><span class="sxs-lookup"><span data-stu-id="392cf-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="392cf-195">所示代码将自定义值提供程序置于所有内置值提供程序之后。</span><span class="sxs-lookup"><span data-stu-id="392cf-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="392cf-196">要将其置于列表中的首位，请调用 `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` 而不是 `Add`。</span><span class="sxs-lookup"><span data-stu-id="392cf-196">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="392cf-197">不存在模型属性的源</span><span class="sxs-lookup"><span data-stu-id="392cf-197">No source for a model property</span></span>

<span data-ttu-id="392cf-198">默认情况下，如果找不到模型属性的值，则不会创建模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="392cf-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="392cf-199">该属性设置为 NULL 或默认值：</span><span class="sxs-lookup"><span data-stu-id="392cf-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="392cf-200">可以为 Null 的简单类型设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="392cf-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="392cf-201">不可以为 Null 的值类型设置为 `default(T)`。</span><span class="sxs-lookup"><span data-stu-id="392cf-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="392cf-202">例如，参数 `int id` 设置为 0。</span><span class="sxs-lookup"><span data-stu-id="392cf-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="392cf-203">对于复杂类型，模型绑定使用默认构造函数来创建实例，而不设置属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="392cf-204">数组设置为 `Array.Empty<T>()`，但 `byte[]` 数组设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="392cf-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="392cf-205">如果在模型属性的窗体字段中未找到任何内容时模型状态应失效，请使用 [`[BindRequired]`](#bindrequired-attribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="392cf-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="392cf-206">请注意，此 `[BindRequired]` 行为适用于发布的表单数据中的模型绑定，而不适用于请求正文中的 JSON 或 XML 数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="392cf-207">请求正文数据由[输入格式化程序](#input-formatters)进行处理。</span><span class="sxs-lookup"><span data-stu-id="392cf-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="392cf-208">类型转换错误</span><span class="sxs-lookup"><span data-stu-id="392cf-208">Type conversion errors</span></span>

<span data-ttu-id="392cf-209">如果找到源，但无法将其转换为目标类型，则模型状态将被标记为无效。</span><span class="sxs-lookup"><span data-stu-id="392cf-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="392cf-210">目标参数或属性设置为 NULL 或默认值，如上一部分所述。</span><span class="sxs-lookup"><span data-stu-id="392cf-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="392cf-211">在具有 `[ApiController]` 属性的 API 控制器中，无效的模型状态会导致自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="392cf-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="392cf-212">在 :::no-loc(Razor)::: 页面中，重新显示页面并显示一条错误消息：</span><span class="sxs-lookup"><span data-stu-id="392cf-212">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="392cf-213">客户端验证将捕获大多数提交到 :::no-loc(Razor)::: 页面窗体的错误数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-213">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="392cf-214">此验证使得先前突出显示的代码难以被触发。</span><span class="sxs-lookup"><span data-stu-id="392cf-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="392cf-215">示例应用包含一个“提交无效日期”按钮，该按钮将错误数据置于“雇用日期”字段中并提交表单。</span><span class="sxs-lookup"><span data-stu-id="392cf-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="392cf-216">此按钮显示在发生数据转换错误时用于重新显示页的代码将如何工作。</span><span class="sxs-lookup"><span data-stu-id="392cf-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="392cf-217">在使用先前的代码重新显示页时，表单域中不会显示无效的输入。</span><span class="sxs-lookup"><span data-stu-id="392cf-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="392cf-218">这是因为模型属性已设置为 NULL 或默认值。</span><span class="sxs-lookup"><span data-stu-id="392cf-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="392cf-219">无效输入会出现在错误消息中。</span><span class="sxs-lookup"><span data-stu-id="392cf-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="392cf-220">但是，如果要在表单域中重新显示错误数据，可以考虑将模型属性设置为字符串并手动执行数据转换。</span><span class="sxs-lookup"><span data-stu-id="392cf-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="392cf-221">如果不希望发生类型转换错误导致模型状态错误的情况，建议使用相同的策略。</span><span class="sxs-lookup"><span data-stu-id="392cf-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="392cf-222">在这种情况下，将模型属性设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="392cf-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="392cf-223">简单类型</span><span class="sxs-lookup"><span data-stu-id="392cf-223">Simple types</span></span>

<span data-ttu-id="392cf-224">模型绑定器可以将源字符串转换为以下简单类型：</span><span class="sxs-lookup"><span data-stu-id="392cf-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="392cf-225">布尔值</span><span class="sxs-lookup"><span data-stu-id="392cf-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="392cf-226">[字节](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="392cf-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="392cf-227">Char</span><span class="sxs-lookup"><span data-stu-id="392cf-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="392cf-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="392cf-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="392cf-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="392cf-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="392cf-230">小数</span><span class="sxs-lookup"><span data-stu-id="392cf-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="392cf-231">双精度</span><span class="sxs-lookup"><span data-stu-id="392cf-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="392cf-232">枚举</span><span class="sxs-lookup"><span data-stu-id="392cf-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="392cf-233">Guid.empty</span><span class="sxs-lookup"><span data-stu-id="392cf-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="392cf-234">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="392cf-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="392cf-235">单精度</span><span class="sxs-lookup"><span data-stu-id="392cf-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="392cf-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="392cf-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="392cf-237">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="392cf-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="392cf-238">Uri</span><span class="sxs-lookup"><span data-stu-id="392cf-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="392cf-239">版本</span><span class="sxs-lookup"><span data-stu-id="392cf-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="392cf-240">复杂类型</span><span class="sxs-lookup"><span data-stu-id="392cf-240">Complex types</span></span>

<span data-ttu-id="392cf-241">复杂类型必须具有要绑定的公共默认构造函数和公共可写属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="392cf-242">进行模型绑定时，将使用公共默认构造函数来实例化类。</span><span class="sxs-lookup"><span data-stu-id="392cf-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="392cf-243">对于复杂类型的每个属性，模型绑定会查找名称模式 prefix.property_name 的源。</span><span class="sxs-lookup"><span data-stu-id="392cf-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="392cf-244">如果未找到，它将仅查找不含前缀的 properties_name。</span><span class="sxs-lookup"><span data-stu-id="392cf-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="392cf-245">对于绑定到参数，前缀是参数名称。</span><span class="sxs-lookup"><span data-stu-id="392cf-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="392cf-246">对于绑定到 `PageModel` 公共属性，前缀是公共属性名称。</span><span class="sxs-lookup"><span data-stu-id="392cf-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="392cf-247">某些属性具有 `Prefix` 属性，让你可以替代参数或属性名称的默认用法。</span><span class="sxs-lookup"><span data-stu-id="392cf-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="392cf-248">例如，假设复杂类型是以下 `Instructor` 类：</span><span class="sxs-lookup"><span data-stu-id="392cf-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="392cf-249">前缀 = 参数名称</span><span class="sxs-lookup"><span data-stu-id="392cf-249">Prefix = parameter name</span></span>

<span data-ttu-id="392cf-250">如果要绑定的模型是一个名为 `instructorToUpdate` 的参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="392cf-251">模型绑定从查找键 `instructorToUpdate.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="392cf-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="392cf-252">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="392cf-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="392cf-253">前缀 = 属性名称</span><span class="sxs-lookup"><span data-stu-id="392cf-253">Prefix = property name</span></span>

<span data-ttu-id="392cf-254">如果要绑定的模型是控制器或 `PageModel` 类的一个名为 `Instructor` 的属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="392cf-255">模型绑定从查找键 `Instructor.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="392cf-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="392cf-256">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="392cf-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="392cf-257">自定义前缀</span><span class="sxs-lookup"><span data-stu-id="392cf-257">Custom prefix</span></span>

<span data-ttu-id="392cf-258">如果要绑定的模型是名为 `instructorToUpdate` 的参数，并且 `Bind` 属性指定 `Instructor` 作为前缀：</span><span class="sxs-lookup"><span data-stu-id="392cf-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="392cf-259">模型绑定从查找键 `Instructor.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="392cf-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="392cf-260">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="392cf-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="392cf-261">复杂类型目标的属性</span><span class="sxs-lookup"><span data-stu-id="392cf-261">Attributes for complex type targets</span></span>

<span data-ttu-id="392cf-262">多个内置属性可用于控制复杂类型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="392cf-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="392cf-263">如果发布的表单数据是值的源，则这些属性会影响模型绑定。</span><span class="sxs-lookup"><span data-stu-id="392cf-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="392cf-264">它们 **不会** 影响输入格式化程序，后者处理已发布的 JSON 和 XML 请求正文。</span><span class="sxs-lookup"><span data-stu-id="392cf-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="392cf-265">输入格式化程序的解释位于[本文后面部分](#input-formatters)。</span><span class="sxs-lookup"><span data-stu-id="392cf-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="392cf-266">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-266">[Bind] attribute</span></span>

<span data-ttu-id="392cf-267">可应用于类或方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="392cf-268">指定模型绑定中应包含的模型属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="392cf-269">`[Bind]` 不 _\*_影响输入_\*_ 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="392cf-270">在下面的示例中，当调用任意处理程序或操作方法时，只绑定 `Instructor` 模型的指定属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="392cf-271">在下面的示例中，当调用 `OnPost` 方法时，只绑定 `Instructor` 模型的指定属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="392cf-272">`[Bind]`特性可用于防范 _create \* 方案中的过多发布。</span><span class="sxs-lookup"><span data-stu-id="392cf-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="392cf-273">由于排除的属性设置为 NULL 或默认值，而不是保持不变，因此它在编辑方案中无法很好地工作。</span><span class="sxs-lookup"><span data-stu-id="392cf-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="392cf-274">为防止过多发布，建议使用视图模型，而不是使用 `[Bind]` 属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="392cf-275">有关详细信息，请参阅[有关过多发布的安全性说明](xref:data/ef-mvc/crud#security-note-about-overposting)。</span><span class="sxs-lookup"><span data-stu-id="392cf-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="392cf-276">[ModelBinder] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="392cf-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> 可应用于类型、属性或参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="392cf-278">它允许指定用于绑定特定实例或类型的模型绑定器的类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="392cf-279">例如：</span><span class="sxs-lookup"><span data-stu-id="392cf-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="392cf-280">`[ModelBinder]`当属性或参数处于模型绑定时，该属性还可用于更改属性或参数的名称：</span><span class="sxs-lookup"><span data-stu-id="392cf-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="392cf-281">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-281">[BindRequired] attribute</span></span>

<span data-ttu-id="392cf-282">只能应用于模型属性，不能应用于方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="392cf-283">如果无法对模型属性进行绑定，则会导致模型绑定添加模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="392cf-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="392cf-284">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="392cf-285">另请参阅[模型验证](xref:mvc/models/validation#required-attribute)中针对 `[Required]` 属性的讨论。</span><span class="sxs-lookup"><span data-stu-id="392cf-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="392cf-286">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-286">[BindNever] attribute</span></span>

<span data-ttu-id="392cf-287">只能应用于模型属性，不能应用于方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="392cf-288">防止模型绑定设置模型的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="392cf-289">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="392cf-290">集合</span><span class="sxs-lookup"><span data-stu-id="392cf-290">Collections</span></span>

<span data-ttu-id="392cf-291">对于是简单类型集合的目标，模型绑定将查找 parameter_name 或 property_name 的匹配项。</span><span class="sxs-lookup"><span data-stu-id="392cf-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="392cf-292">如果找不到匹配项，它将查找某种不含前缀的受支持的格式。</span><span class="sxs-lookup"><span data-stu-id="392cf-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="392cf-293">例如：</span><span class="sxs-lookup"><span data-stu-id="392cf-293">For example:</span></span>

* <span data-ttu-id="392cf-294">假设要绑定的参数是名为 `selectedCourses` 的数组：</span><span class="sxs-lookup"><span data-stu-id="392cf-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="392cf-295">表单或查询字符串数据可以采用以下某种格式：</span><span class="sxs-lookup"><span data-stu-id="392cf-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="392cf-296">只有表单数据支持以下格式：</span><span class="sxs-lookup"><span data-stu-id="392cf-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="392cf-297">对于前面所有的示例格式，模型绑定将两个项的数组传递给 `selectedCourses` 参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="392cf-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="392cf-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="392cf-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="392cf-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="392cf-300">使用下标数字的数据格式 (... [0] ... [1] ...) 必须确保从零开始按顺序进行编号。</span><span class="sxs-lookup"><span data-stu-id="392cf-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="392cf-301">如果下标编号中存在任何间隔，则间隔后的所有项都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="392cf-302">例如，如果下标是 0 和 2，而不是 0 和 1，则第二个项会被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="392cf-303">字典</span><span class="sxs-lookup"><span data-stu-id="392cf-303">Dictionaries</span></span>

<span data-ttu-id="392cf-304">对于 `Dictionary` 目标，模型绑定会查找 parameter_name 或 property_name 的匹配项。</span><span class="sxs-lookup"><span data-stu-id="392cf-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="392cf-305">如果找不到匹配项，它将查找某种不含前缀的受支持的格式。</span><span class="sxs-lookup"><span data-stu-id="392cf-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="392cf-306">例如：</span><span class="sxs-lookup"><span data-stu-id="392cf-306">For example:</span></span>

* <span data-ttu-id="392cf-307">假设目标参数是名为 `selectedCourses` 的 `Dictionary<int, string>`：</span><span class="sxs-lookup"><span data-stu-id="392cf-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="392cf-308">发布的表单或查询字符串数据可以类似于以下某一示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="392cf-309">对于前面所有的示例格式，模型绑定将两个项的字典传递给 `selectedCourses` 参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="392cf-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="392cf-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="392cf-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="392cf-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="392cf-312">构造函数绑定和记录类型</span><span class="sxs-lookup"><span data-stu-id="392cf-312">Constructor binding and record types</span></span>

<span data-ttu-id="392cf-313">模型绑定要求复杂类型具有无参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="392cf-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="392cf-314">`System.Text.Json`和 `Newtonsoft.Json` 基于的输入格式化程序都支持对不具有无参数构造函数的类进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="392cf-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="392cf-315">C # 9 引入了记录类型，这是一个很好的方法，可以在网络上简洁地表示数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="392cf-316">ASP.NET Core 增加了对模型绑定和使用单个构造函数验证记录类型的支持：</span><span class="sxs-lookup"><span data-stu-id="392cf-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="392cf-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="392cf-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="392cf-318">在验证记录类型时，运行时将搜索专用于参数而不是属性的验证元数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="392cf-319">模型绑定路由数据和查询字符串的全球化行为</span><span class="sxs-lookup"><span data-stu-id="392cf-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="392cf-320">ASP.NET Core 路由值提供程序和查询字符串值提供程序：</span><span class="sxs-lookup"><span data-stu-id="392cf-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="392cf-321">将值视为固定区域性。</span><span class="sxs-lookup"><span data-stu-id="392cf-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="392cf-322">URL 的区域性应固定。</span><span class="sxs-lookup"><span data-stu-id="392cf-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="392cf-323">相反，来自窗体数据的值要进行区分区域性的转换。</span><span class="sxs-lookup"><span data-stu-id="392cf-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="392cf-324">这是设计使然，目的是让 URL 可在各个区域设置中共享。</span><span class="sxs-lookup"><span data-stu-id="392cf-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="392cf-325">使 ASP.NET Core 路由值提供程序和查询字符串值提供程序进行区分区域性的转换：</span><span class="sxs-lookup"><span data-stu-id="392cf-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="392cf-326">继承自 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="392cf-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="392cf-327">从 [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) 或 [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) 复制代码</span><span class="sxs-lookup"><span data-stu-id="392cf-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="392cf-328">使用 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) 替换传递给值提供程序构造函数的[区域性值](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)</span><span class="sxs-lookup"><span data-stu-id="392cf-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="392cf-329">将 MVC 选项中的默认值提供程序工厂替换为新的工厂：</span><span class="sxs-lookup"><span data-stu-id="392cf-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="392cf-330">特殊数据类型</span><span class="sxs-lookup"><span data-stu-id="392cf-330">Special data types</span></span>

<span data-ttu-id="392cf-331">模型绑定可以处理某些特殊的数据类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="392cf-332">IFormFile 和 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="392cf-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="392cf-333">HTTP 请求中包含的上传文件。</span><span class="sxs-lookup"><span data-stu-id="392cf-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="392cf-334">还支持多个文件的 `IEnumerable<IFormFile>`。</span><span class="sxs-lookup"><span data-stu-id="392cf-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="392cf-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="392cf-335">CancellationToken</span></span>

<span data-ttu-id="392cf-336">用于取消异步控制器中的活动。</span><span class="sxs-lookup"><span data-stu-id="392cf-336">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="392cf-337">FormCollection</span><span class="sxs-lookup"><span data-stu-id="392cf-337">FormCollection</span></span>

<span data-ttu-id="392cf-338">用于从发布的表单数据中检索所有的值。</span><span class="sxs-lookup"><span data-stu-id="392cf-338">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="392cf-339">输入格式化程序</span><span class="sxs-lookup"><span data-stu-id="392cf-339">Input formatters</span></span>

<span data-ttu-id="392cf-340">请求正文中的数据可以是 JSON、XML 或其他某种格式。</span><span class="sxs-lookup"><span data-stu-id="392cf-340">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="392cf-341">要分析此数据，模型绑定会使用配置为处理特定内容类型的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-341">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="392cf-342">默认情况下，ASP.NET Core 包括用于处理 JSON 数据的基于 JSON 的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-342">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="392cf-343">可以为其他内容类型添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-343">You can add other formatters for other content types.</span></span>

<span data-ttu-id="392cf-344">ASP.NET Core 基于 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性来选择输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-344">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="392cf-345">如果没有属性，它将使用 [Content-Type 标头](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。</span><span class="sxs-lookup"><span data-stu-id="392cf-345">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="392cf-346">要使用内置 XML 输入格式化程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="392cf-346">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="392cf-347">安装 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="392cf-347">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="392cf-348">在 `Startup.ConfigureServices` 中，调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。</span><span class="sxs-lookup"><span data-stu-id="392cf-348">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="392cf-349">将 `Consumes` 属性应用于应在请求正文中使用 XML 的控制器类或操作方法。</span><span class="sxs-lookup"><span data-stu-id="392cf-349">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="392cf-350">有关更多信息，请参阅 [XML 序列化简介](/dotnet/standard/serialization/introducing-xml-serialization)。</span><span class="sxs-lookup"><span data-stu-id="392cf-350">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="392cf-351">使用输入格式化程序自定义模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-351">Customize model binding with input formatters</span></span>

<span data-ttu-id="392cf-352">由输入格式化程序完全负责从请求正文读取数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-352">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="392cf-353">若要自定义此过程，请配置输入格式化程序使用的 API。</span><span class="sxs-lookup"><span data-stu-id="392cf-353">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="392cf-354">此部分介绍如何自定义基于 `System.Text.Json` 的输入格式化程序，以了解自定义类型 `ObjectId`。</span><span class="sxs-lookup"><span data-stu-id="392cf-354">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="392cf-355">以包含自定义 `ObjectId` 属性 `Id` 的模型为例：</span><span class="sxs-lookup"><span data-stu-id="392cf-355">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="392cf-356">使用 `System.Text.Json` 时，若要自定义模型绑定过程，请创建派生自 <xref:System.Text.Json.Serialization.JsonConverter%601> 的类：</span><span class="sxs-lookup"><span data-stu-id="392cf-356">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="392cf-357">将 <xref:System.Text.Json.Serialization.JsonConverterAttribute> 属性应用到此类型，以使用自定义转换器。</span><span class="sxs-lookup"><span data-stu-id="392cf-357">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="392cf-358">在下面的示例中，为 `ObjectId` 类型配置了 `ObjectIdConverter` 来作为其自定义转换器：</span><span class="sxs-lookup"><span data-stu-id="392cf-358">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="392cf-359">有关详细信息，请参阅[如何编写自定义转换器](/dotnet/standard/serialization/system-text-json-converters-how-to)。</span><span class="sxs-lookup"><span data-stu-id="392cf-359">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="392cf-360">从模型绑定中排除指定类型</span><span class="sxs-lookup"><span data-stu-id="392cf-360">Exclude specified types from model binding</span></span>

<span data-ttu-id="392cf-361">模型绑定和验证系统的行为由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 驱动。</span><span class="sxs-lookup"><span data-stu-id="392cf-361">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="392cf-362">可通过向 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) 添加详细信息提供程序来自定义 `ModelMetadata`。</span><span class="sxs-lookup"><span data-stu-id="392cf-362">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="392cf-363">内置详细信息提供程序可用于禁用指定类型的模型绑定或验证。</span><span class="sxs-lookup"><span data-stu-id="392cf-363">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="392cf-364">要禁用指定类型的所有模型的模型绑定，请在 `Startup.ConfigureServices` 中添加 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="392cf-364">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="392cf-365">例如，禁用对 `System.Version` 类型的所有模型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="392cf-365">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="392cf-366">要禁用指定类型的属性的验证，请在 `Startup.ConfigureServices` 中添加 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="392cf-366">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="392cf-367">例如，禁用对 `System.Guid` 类型的属性的验证：</span><span class="sxs-lookup"><span data-stu-id="392cf-367">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="392cf-368">自定义模型绑定器</span><span class="sxs-lookup"><span data-stu-id="392cf-368">Custom model binders</span></span>

<span data-ttu-id="392cf-369">通过编写自定义模型绑定器，并使用 `[ModelBinder]` 属性为给定目标选择该模型绑定器，可扩展模型绑定。</span><span class="sxs-lookup"><span data-stu-id="392cf-369">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="392cf-370">详细了解[自定义模型绑定](xref:mvc/advanced/custom-model-binding)。</span><span class="sxs-lookup"><span data-stu-id="392cf-370">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="392cf-371">手动模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-371">Manual model binding</span></span> 

<span data-ttu-id="392cf-372">可以使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法手动调用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="392cf-372">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="392cf-373">`ControllerBase` 和 `PageModel` 类上均定义了此方法。</span><span class="sxs-lookup"><span data-stu-id="392cf-373">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="392cf-374">方法重载允许指定要使用的前缀和值提供程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-374">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="392cf-375">如果模型绑定失败，该方法返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="392cf-375">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="392cf-376">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-376">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="392cf-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 使用值提供程序从窗体正文、查询字符串和路由数据获取数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="392cf-378">`TryUpdateModelAsync` 通常有以下特点：</span><span class="sxs-lookup"><span data-stu-id="392cf-378">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="392cf-379">与 :::no-loc(Razor)::: 使用控制器和视图的页面和 MVC 应用一起使用，以防止过度发布。</span><span class="sxs-lookup"><span data-stu-id="392cf-379">Used with :::no-loc(Razor)::: Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="392cf-380">不用于 Web API（除非窗体数据、查询字符串和路由数据使用它）。</span><span class="sxs-lookup"><span data-stu-id="392cf-380">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="392cf-381">使用 JSON 的 Web API 终结点使用[输入格式化程序](#input-formatters)将请求正文反序列化为对象。</span><span class="sxs-lookup"><span data-stu-id="392cf-381">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="392cf-382">有关详细信息，请参阅 [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)。</span><span class="sxs-lookup"><span data-stu-id="392cf-382">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="392cf-383">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-383">[FromServices] attribute</span></span>

<span data-ttu-id="392cf-384">此属性的名称遵循指定数据源的模型绑定属性的模式。</span><span class="sxs-lookup"><span data-stu-id="392cf-384">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="392cf-385">但这与绑定来自值提供程序的数据无关。</span><span class="sxs-lookup"><span data-stu-id="392cf-385">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="392cf-386">它从[依赖关系注入](xref:fundamentals/dependency-injection)容器中获取类型的实例。</span><span class="sxs-lookup"><span data-stu-id="392cf-386">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="392cf-387">其目的在于，在仅当调用特定方法时需要服务的情况下，提供构造函数注入的替代方法。</span><span class="sxs-lookup"><span data-stu-id="392cf-387">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="392cf-388">其他资源</span><span class="sxs-lookup"><span data-stu-id="392cf-388">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="392cf-389">本文解释了模型绑定的定义、模型绑定的工作原理，以及如何自定义模型绑定的行为。</span><span class="sxs-lookup"><span data-stu-id="392cf-389">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="392cf-390">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="392cf-390">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="392cf-391">什么是模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-391">What is Model binding</span></span>

<span data-ttu-id="392cf-392">控制器和 :::no-loc(Razor)::: 页面处理来自 HTTP 请求的数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-392">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="392cf-393">例如，路由数据可以提供一个记录键，而发布的表单域可以为模型的属性提供一个值。</span><span class="sxs-lookup"><span data-stu-id="392cf-393">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="392cf-394">编写代码以检索这些值，并将其从字符串转换为 .NET 类型不仅繁琐，而且还容易出错。</span><span class="sxs-lookup"><span data-stu-id="392cf-394">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="392cf-395">模型绑定会自动化该过程。</span><span class="sxs-lookup"><span data-stu-id="392cf-395">Model binding automates this process.</span></span> <span data-ttu-id="392cf-396">模型绑定系统：</span><span class="sxs-lookup"><span data-stu-id="392cf-396">The model binding system:</span></span>

* <span data-ttu-id="392cf-397">从各种源（如路由数据、表单域和查询字符串）中检索数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-397">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="392cf-398">:::no-loc(Razor):::在方法参数和公共属性中向控制器和页面提供数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-398">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="392cf-399">将字符串数据转换为 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-399">Converts string data to .NET types.</span></span>
* <span data-ttu-id="392cf-400">更新复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-400">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="392cf-401">示例</span><span class="sxs-lookup"><span data-stu-id="392cf-401">Example</span></span>

<span data-ttu-id="392cf-402">假设有以下操作方法：</span><span class="sxs-lookup"><span data-stu-id="392cf-402">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="392cf-403">并且应用收到一个带有以下 URL 的请求：</span><span class="sxs-lookup"><span data-stu-id="392cf-403">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="392cf-404">在路由系统选择该操作方法之后，模型绑定执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="392cf-404">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="392cf-405">查找 `GetByID` 的第一个参数，该参数是一个名为 `id` 的整数。</span><span class="sxs-lookup"><span data-stu-id="392cf-405">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="392cf-406">查找 HTTP 请求中的可用源，并在路由数据中查找 `id` =“2”。</span><span class="sxs-lookup"><span data-stu-id="392cf-406">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="392cf-407">将字符串“2”转换为整数 2。</span><span class="sxs-lookup"><span data-stu-id="392cf-407">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="392cf-408">查找 `GetByID` 的下一个参数，该参数是一个名为 `dogsOnly` 的布尔值。</span><span class="sxs-lookup"><span data-stu-id="392cf-408">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="392cf-409">查找源，并在查询字符串中查找“DogsOnly=true”。</span><span class="sxs-lookup"><span data-stu-id="392cf-409">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="392cf-410">名称匹配不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="392cf-410">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="392cf-411">将字符串“true”转换为布尔值 `true`。</span><span class="sxs-lookup"><span data-stu-id="392cf-411">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="392cf-412">然后，该框架会调用 `GetById` 方法，为 `id` 参数传入 2，并为 `dogsOnly` 参数传入 `true`。</span><span class="sxs-lookup"><span data-stu-id="392cf-412">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="392cf-413">在前面的示例中，模型绑定目标是简单类型的方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-413">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="392cf-414">目标也可以是复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-414">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="392cf-415">成功绑定每个属性后，将对属性进行[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="392cf-415">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="392cf-416">有关绑定到模型的数据以及任意绑定或验证错误的记录都存储在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 中。</span><span class="sxs-lookup"><span data-stu-id="392cf-416">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="392cf-417">为查明该过程是否已成功，应用会检查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 标志。</span><span class="sxs-lookup"><span data-stu-id="392cf-417">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="392cf-418">目标</span><span class="sxs-lookup"><span data-stu-id="392cf-418">Targets</span></span>

<span data-ttu-id="392cf-419">模型绑定尝试查找以下类型目标的值：</span><span class="sxs-lookup"><span data-stu-id="392cf-419">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="392cf-420">将请求路由到的控制器操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-420">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="392cf-421">:::no-loc(Razor):::请求路由到的页处理程序方法的参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-421">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="392cf-422">控制器或 `PageModel` 类的公共属性（若由特性指定）。</span><span class="sxs-lookup"><span data-stu-id="392cf-422">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="392cf-423">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-423">[BindProperty] attribute</span></span>

<span data-ttu-id="392cf-424">可应用于控制器或 `PageModel` 类的公共属性，从而使模型绑定以该属性为目标：</span><span class="sxs-lookup"><span data-stu-id="392cf-424">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="392cf-425">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-425">[BindProperties] attribute</span></span>

<span data-ttu-id="392cf-426">可在 ASP.NET Core 2.1 及更高版本中获得。</span><span class="sxs-lookup"><span data-stu-id="392cf-426">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="392cf-427">可应用于控制器或 `PageModel` 类，以使模型绑定以该类的所有公共属性为目标：</span><span class="sxs-lookup"><span data-stu-id="392cf-427">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="392cf-428">HTTP GET 请求的模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-428">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="392cf-429">默认情况下，不绑定 HTTP GET 请求的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-429">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="392cf-430">通常，GET 请求只需一个记录 ID 参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-430">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="392cf-431">记录 ID 用于查找数据库中的项。</span><span class="sxs-lookup"><span data-stu-id="392cf-431">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="392cf-432">因此，无需绑定包含模型实例的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-432">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="392cf-433">在需要将属性绑定到 GET 请求中的数据的情况下，请将 `SupportsGet` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="392cf-433">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="392cf-434">源</span><span class="sxs-lookup"><span data-stu-id="392cf-434">Sources</span></span>

<span data-ttu-id="392cf-435">默认情况下，模型绑定以键值对的形式从 HTTP 请求中的以下源中获取数据：</span><span class="sxs-lookup"><span data-stu-id="392cf-435">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="392cf-436">表单域</span><span class="sxs-lookup"><span data-stu-id="392cf-436">Form fields</span></span>
1. <span data-ttu-id="392cf-437">请求正文（对于[具有 [ApiController] 属性的控制器](xref:web-api/index#binding-source-parameter-inference)。）</span><span class="sxs-lookup"><span data-stu-id="392cf-437">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="392cf-438">路由数据</span><span class="sxs-lookup"><span data-stu-id="392cf-438">Route data</span></span>
1. <span data-ttu-id="392cf-439">查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="392cf-439">Query string parameters</span></span>
1. <span data-ttu-id="392cf-440">上传的文件</span><span class="sxs-lookup"><span data-stu-id="392cf-440">Uploaded files</span></span>

<span data-ttu-id="392cf-441">对于每个目标参数或属性，按照之前列表中指示的顺序扫描源。</span><span class="sxs-lookup"><span data-stu-id="392cf-441">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="392cf-442">有几个例外情况：</span><span class="sxs-lookup"><span data-stu-id="392cf-442">There are a few exceptions:</span></span>

* <span data-ttu-id="392cf-443">路由数据和查询字符串值仅用于简单类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-443">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="392cf-444">上传的文件仅绑定到实现 `IFormFile` 或 `IEnumerable<IFormFile>` 的目标类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-444">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="392cf-445">如果默认源不正确，请使用下列属性之一来指定源：</span><span class="sxs-lookup"><span data-stu-id="392cf-445">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="392cf-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -从查询字符串获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="392cf-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -从路由数据中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="392cf-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -从已发布的表单字段中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="392cf-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -从请求正文中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="392cf-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -从 HTTP 标头中获取值。</span><span class="sxs-lookup"><span data-stu-id="392cf-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="392cf-451">这些属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-451">These attributes:</span></span>

* <span data-ttu-id="392cf-452">分别添加到模型属性（而不是模型类），如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="392cf-452">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="392cf-453">选择性地在构造函数中接受模型名称值。</span><span class="sxs-lookup"><span data-stu-id="392cf-453">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="392cf-454">提供此选项的目的是应对属性名称与请求中的值不匹配的情况。</span><span class="sxs-lookup"><span data-stu-id="392cf-454">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="392cf-455">例如，请求中的值可能是名称中带有连字符的标头，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="392cf-455">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="392cf-456">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-456">[FromBody] attribute</span></span>

<span data-ttu-id="392cf-457">将 `[FromBody]` 特性应用于一个参数，以便从一个 HTTP 请求的正文填充其属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-457">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="392cf-458">ASP.NET Core 运行时将读取正文的责任委托给输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-458">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="392cf-459">输入格式化程序的解释位于[本文后面部分](#input-formatters)。</span><span class="sxs-lookup"><span data-stu-id="392cf-459">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="392cf-460">将 `[FromBody]` 应用于复杂类型参数时，应用于其属性的任何绑定源属性都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-460">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="392cf-461">例如，以下 `Create` 操作指定从正文填充其 `pet` 参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-461">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="392cf-462">`Pet` 类指定从查询字符串参数填充其 `Breed` 属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-462">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="392cf-463">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="392cf-463">In the preceding example:</span></span>

* <span data-ttu-id="392cf-464">`[FromQuery]` 特性被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-464">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="392cf-465">`Breed` 属性未从查询字符串参数进行填充。</span><span class="sxs-lookup"><span data-stu-id="392cf-465">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="392cf-466">输入格式化程序只读取正文，不了解绑定源特性。</span><span class="sxs-lookup"><span data-stu-id="392cf-466">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="392cf-467">如果在正文中找到合适的值，则使用该值填充 `Breed` 属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-467">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="392cf-468">不要将 `[FromBody]` 应用于每个操作方法的多个参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-468">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="392cf-469">输入格式化程序读取请求流后，无法再次读取该流以绑定其他 `[FromBody]` 参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-469">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="392cf-470">其他源</span><span class="sxs-lookup"><span data-stu-id="392cf-470">Additional sources</span></span>

<span data-ttu-id="392cf-471">源数据由“值提供程序”提供给模型绑定系统。</span><span class="sxs-lookup"><span data-stu-id="392cf-471">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="392cf-472">你可以编写并注册自定义值提供程序，这些提供程序从其他源中获取用于模型绑定的数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-472">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="392cf-473">例如，你可能需要来自 :::no-loc(cookie)::: 或会话状态的数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-473">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="392cf-474">要从新的源中获取数据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="392cf-474">To get data from a new source:</span></span>

* <span data-ttu-id="392cf-475">创建用于实现 `IValueProvider` 的类。</span><span class="sxs-lookup"><span data-stu-id="392cf-475">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="392cf-476">创建用于实现 `IValueProviderFactory` 的类。</span><span class="sxs-lookup"><span data-stu-id="392cf-476">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="392cf-477">在 `Startup.ConfigureServices` 中注册工厂类。</span><span class="sxs-lookup"><span data-stu-id="392cf-477">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="392cf-478">该示例应用包含一个 [值提供程序](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) 和一个 [工厂](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) 示例，可从 s 中获取值 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="392cf-478">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="392cf-479">以下是 `Startup.ConfigureServices` 中的注册代码：</span><span class="sxs-lookup"><span data-stu-id="392cf-479">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="392cf-480">所示代码将自定义值提供程序置于所有内置值提供程序之后。</span><span class="sxs-lookup"><span data-stu-id="392cf-480">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="392cf-481">要将其置于列表中的首位，请调用 `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` 而不是 `Add`。</span><span class="sxs-lookup"><span data-stu-id="392cf-481">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="392cf-482">不存在模型属性的源</span><span class="sxs-lookup"><span data-stu-id="392cf-482">No source for a model property</span></span>

<span data-ttu-id="392cf-483">默认情况下，如果找不到模型属性的值，则不会创建模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="392cf-483">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="392cf-484">该属性设置为 NULL 或默认值：</span><span class="sxs-lookup"><span data-stu-id="392cf-484">The property is set to null or a default value:</span></span>

* <span data-ttu-id="392cf-485">可以为 Null 的简单类型设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="392cf-485">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="392cf-486">不可以为 Null 的值类型设置为 `default(T)`。</span><span class="sxs-lookup"><span data-stu-id="392cf-486">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="392cf-487">例如，参数 `int id` 设置为 0。</span><span class="sxs-lookup"><span data-stu-id="392cf-487">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="392cf-488">对于复杂类型，模型绑定使用默认构造函数来创建实例，而不设置属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-488">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="392cf-489">数组设置为 `Array.Empty<T>()`，但 `byte[]` 数组设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="392cf-489">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="392cf-490">如果在模型属性的窗体字段中未找到任何内容时模型状态应失效，请使用 [`[BindRequired]`](#bindrequired-attribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="392cf-490">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="392cf-491">请注意，此 `[BindRequired]` 行为适用于发布的表单数据中的模型绑定，而不适用于请求正文中的 JSON 或 XML 数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-491">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="392cf-492">请求正文数据由[输入格式化程序](#input-formatters)进行处理。</span><span class="sxs-lookup"><span data-stu-id="392cf-492">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="392cf-493">类型转换错误</span><span class="sxs-lookup"><span data-stu-id="392cf-493">Type conversion errors</span></span>

<span data-ttu-id="392cf-494">如果找到源，但无法将其转换为目标类型，则模型状态将被标记为无效。</span><span class="sxs-lookup"><span data-stu-id="392cf-494">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="392cf-495">目标参数或属性设置为 NULL 或默认值，如上一部分所述。</span><span class="sxs-lookup"><span data-stu-id="392cf-495">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="392cf-496">在具有 `[ApiController]` 属性的 API 控制器中，无效的模型状态会导致自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="392cf-496">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="392cf-497">在 :::no-loc(Razor)::: 页面中，重新显示页面并显示一条错误消息：</span><span class="sxs-lookup"><span data-stu-id="392cf-497">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="392cf-498">客户端验证将捕获大多数提交到 :::no-loc(Razor)::: 页面窗体的错误数据。</span><span class="sxs-lookup"><span data-stu-id="392cf-498">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="392cf-499">此验证使得先前突出显示的代码难以被触发。</span><span class="sxs-lookup"><span data-stu-id="392cf-499">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="392cf-500">示例应用包含一个“提交无效日期”按钮，该按钮将错误数据置于“雇用日期”字段中并提交表单。</span><span class="sxs-lookup"><span data-stu-id="392cf-500">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="392cf-501">此按钮显示在发生数据转换错误时用于重新显示页的代码将如何工作。</span><span class="sxs-lookup"><span data-stu-id="392cf-501">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="392cf-502">在使用先前的代码重新显示页时，表单域中不会显示无效的输入。</span><span class="sxs-lookup"><span data-stu-id="392cf-502">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="392cf-503">这是因为模型属性已设置为 NULL 或默认值。</span><span class="sxs-lookup"><span data-stu-id="392cf-503">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="392cf-504">无效输入会出现在错误消息中。</span><span class="sxs-lookup"><span data-stu-id="392cf-504">The invalid input does appear in an error message.</span></span> <span data-ttu-id="392cf-505">但是，如果要在表单域中重新显示错误数据，可以考虑将模型属性设置为字符串并手动执行数据转换。</span><span class="sxs-lookup"><span data-stu-id="392cf-505">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="392cf-506">如果不希望发生类型转换错误导致模型状态错误的情况，建议使用相同的策略。</span><span class="sxs-lookup"><span data-stu-id="392cf-506">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="392cf-507">在这种情况下，将模型属性设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="392cf-507">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="392cf-508">简单类型</span><span class="sxs-lookup"><span data-stu-id="392cf-508">Simple types</span></span>

<span data-ttu-id="392cf-509">模型绑定器可以将源字符串转换为以下简单类型：</span><span class="sxs-lookup"><span data-stu-id="392cf-509">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="392cf-510">布尔值</span><span class="sxs-lookup"><span data-stu-id="392cf-510">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="392cf-511">[字节](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="392cf-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="392cf-512">Char</span><span class="sxs-lookup"><span data-stu-id="392cf-512">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="392cf-513">DateTime</span><span class="sxs-lookup"><span data-stu-id="392cf-513">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="392cf-514">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="392cf-514">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="392cf-515">小数</span><span class="sxs-lookup"><span data-stu-id="392cf-515">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="392cf-516">双精度</span><span class="sxs-lookup"><span data-stu-id="392cf-516">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="392cf-517">枚举</span><span class="sxs-lookup"><span data-stu-id="392cf-517">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="392cf-518">Guid.empty</span><span class="sxs-lookup"><span data-stu-id="392cf-518">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="392cf-519">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="392cf-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="392cf-520">单精度</span><span class="sxs-lookup"><span data-stu-id="392cf-520">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="392cf-521">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="392cf-521">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="392cf-522">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="392cf-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="392cf-523">Uri</span><span class="sxs-lookup"><span data-stu-id="392cf-523">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="392cf-524">版本</span><span class="sxs-lookup"><span data-stu-id="392cf-524">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="392cf-525">复杂类型</span><span class="sxs-lookup"><span data-stu-id="392cf-525">Complex types</span></span>

<span data-ttu-id="392cf-526">复杂类型必须具有要绑定的公共默认构造函数和公共可写属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-526">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="392cf-527">进行模型绑定时，将使用公共默认构造函数来实例化类。</span><span class="sxs-lookup"><span data-stu-id="392cf-527">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="392cf-528">对于复杂类型的每个属性，模型绑定会查找名称模式 prefix.property_name 的源。</span><span class="sxs-lookup"><span data-stu-id="392cf-528">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="392cf-529">如果未找到，它将仅查找不含前缀的 properties_name。</span><span class="sxs-lookup"><span data-stu-id="392cf-529">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="392cf-530">对于绑定到参数，前缀是参数名称。</span><span class="sxs-lookup"><span data-stu-id="392cf-530">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="392cf-531">对于绑定到 `PageModel` 公共属性，前缀是公共属性名称。</span><span class="sxs-lookup"><span data-stu-id="392cf-531">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="392cf-532">某些属性具有 `Prefix` 属性，让你可以替代参数或属性名称的默认用法。</span><span class="sxs-lookup"><span data-stu-id="392cf-532">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="392cf-533">例如，假设复杂类型是以下 `Instructor` 类：</span><span class="sxs-lookup"><span data-stu-id="392cf-533">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="392cf-534">前缀 = 参数名称</span><span class="sxs-lookup"><span data-stu-id="392cf-534">Prefix = parameter name</span></span>

<span data-ttu-id="392cf-535">如果要绑定的模型是一个名为 `instructorToUpdate` 的参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-535">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="392cf-536">模型绑定从查找键 `instructorToUpdate.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="392cf-536">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="392cf-537">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="392cf-537">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="392cf-538">前缀 = 属性名称</span><span class="sxs-lookup"><span data-stu-id="392cf-538">Prefix = property name</span></span>

<span data-ttu-id="392cf-539">如果要绑定的模型是控制器或 `PageModel` 类的一个名为 `Instructor` 的属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-539">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="392cf-540">模型绑定从查找键 `Instructor.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="392cf-540">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="392cf-541">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="392cf-541">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="392cf-542">自定义前缀</span><span class="sxs-lookup"><span data-stu-id="392cf-542">Custom prefix</span></span>

<span data-ttu-id="392cf-543">如果要绑定的模型是名为 `instructorToUpdate` 的参数，并且 `Bind` 属性指定 `Instructor` 作为前缀：</span><span class="sxs-lookup"><span data-stu-id="392cf-543">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="392cf-544">模型绑定从查找键 `Instructor.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="392cf-544">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="392cf-545">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="392cf-545">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="392cf-546">复杂类型目标的属性</span><span class="sxs-lookup"><span data-stu-id="392cf-546">Attributes for complex type targets</span></span>

<span data-ttu-id="392cf-547">多个内置属性可用于控制复杂类型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="392cf-547">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="392cf-548">如果发布的表单数据是值的源，则这些属性会影响模型绑定。</span><span class="sxs-lookup"><span data-stu-id="392cf-548">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="392cf-549">它们不会影响处理发布的 JSON 和 XML 请求正文的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-549">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="392cf-550">输入格式化程序的解释位于[本文后面部分](#input-formatters)。</span><span class="sxs-lookup"><span data-stu-id="392cf-550">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="392cf-551">另请参阅[模型验证](xref:mvc/models/validation#required-attribute)中针对 `[Required]` 属性的讨论。</span><span class="sxs-lookup"><span data-stu-id="392cf-551">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="392cf-552">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-552">[BindRequired] attribute</span></span>

<span data-ttu-id="392cf-553">只能应用于模型属性，不能应用于方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-553">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="392cf-554">如果无法对模型属性进行绑定，则会导致模型绑定添加模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="392cf-554">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="392cf-555">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-555">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="392cf-556">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-556">[BindNever] attribute</span></span>

<span data-ttu-id="392cf-557">只能应用于模型属性，不能应用于方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-557">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="392cf-558">防止模型绑定设置模型的属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-558">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="392cf-559">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-559">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="392cf-560">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-560">[Bind] attribute</span></span>

<span data-ttu-id="392cf-561">可应用于类或方法参数。</span><span class="sxs-lookup"><span data-stu-id="392cf-561">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="392cf-562">指定模型绑定中应包含的模型属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-562">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="392cf-563">在下面的示例中，当调用任意处理程序或操作方法时，只绑定 `Instructor` 模型的指定属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-563">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="392cf-564">在下面的示例中，当调用 `OnPost` 方法时，只绑定 `Instructor` 模型的指定属性：</span><span class="sxs-lookup"><span data-stu-id="392cf-564">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="392cf-565">`[Bind]` 属性可用于防止“创建”方案中的过多发布情况。</span><span class="sxs-lookup"><span data-stu-id="392cf-565">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="392cf-566">由于排除的属性设置为 NULL 或默认值，而不是保持不变，因此它在编辑方案中无法很好地工作。</span><span class="sxs-lookup"><span data-stu-id="392cf-566">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="392cf-567">为防止过多发布，建议使用视图模型，而不是使用 `[Bind]` 属性。</span><span class="sxs-lookup"><span data-stu-id="392cf-567">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="392cf-568">有关详细信息，请参阅[有关过多发布的安全性说明](xref:data/ef-mvc/crud#security-note-about-overposting)。</span><span class="sxs-lookup"><span data-stu-id="392cf-568">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="392cf-569">集合</span><span class="sxs-lookup"><span data-stu-id="392cf-569">Collections</span></span>

<span data-ttu-id="392cf-570">对于是简单类型集合的目标，模型绑定将查找 parameter_name 或 property_name 的匹配项。</span><span class="sxs-lookup"><span data-stu-id="392cf-570">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="392cf-571">如果找不到匹配项，它将查找某种不含前缀的受支持的格式。</span><span class="sxs-lookup"><span data-stu-id="392cf-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="392cf-572">例如：</span><span class="sxs-lookup"><span data-stu-id="392cf-572">For example:</span></span>

* <span data-ttu-id="392cf-573">假设要绑定的参数是名为 `selectedCourses` 的数组：</span><span class="sxs-lookup"><span data-stu-id="392cf-573">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="392cf-574">表单或查询字符串数据可以采用以下某种格式：</span><span class="sxs-lookup"><span data-stu-id="392cf-574">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="392cf-575">只有表单数据支持以下格式：</span><span class="sxs-lookup"><span data-stu-id="392cf-575">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="392cf-576">对于前面所有的示例格式，模型绑定将两个项的数组传递给 `selectedCourses` 参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-576">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="392cf-577">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="392cf-577">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="392cf-578">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="392cf-578">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="392cf-579">使用下标数字的数据格式 (... [0] ... [1] ...) 必须确保从零开始按顺序进行编号。</span><span class="sxs-lookup"><span data-stu-id="392cf-579">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="392cf-580">如果下标编号中存在任何间隔，则间隔后的所有项都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-580">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="392cf-581">例如，如果下标是 0 和 2，而不是 0 和 1，则第二个项会被忽略。</span><span class="sxs-lookup"><span data-stu-id="392cf-581">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="392cf-582">字典</span><span class="sxs-lookup"><span data-stu-id="392cf-582">Dictionaries</span></span>

<span data-ttu-id="392cf-583">对于 `Dictionary` 目标，模型绑定会查找 parameter_name 或 property_name 的匹配项。</span><span class="sxs-lookup"><span data-stu-id="392cf-583">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="392cf-584">如果找不到匹配项，它将查找某种不含前缀的受支持的格式。</span><span class="sxs-lookup"><span data-stu-id="392cf-584">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="392cf-585">例如：</span><span class="sxs-lookup"><span data-stu-id="392cf-585">For example:</span></span>

* <span data-ttu-id="392cf-586">假设目标参数是名为 `selectedCourses` 的 `Dictionary<int, string>`：</span><span class="sxs-lookup"><span data-stu-id="392cf-586">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="392cf-587">发布的表单或查询字符串数据可以类似于以下某一示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-587">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="392cf-588">对于前面所有的示例格式，模型绑定将两个项的字典传递给 `selectedCourses` 参数：</span><span class="sxs-lookup"><span data-stu-id="392cf-588">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="392cf-589">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="392cf-589">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="392cf-590">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="392cf-590">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="392cf-591">模型绑定路由数据和查询字符串的全球化行为</span><span class="sxs-lookup"><span data-stu-id="392cf-591">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="392cf-592">ASP.NET Core 路由值提供程序和查询字符串值提供程序：</span><span class="sxs-lookup"><span data-stu-id="392cf-592">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="392cf-593">将值视为固定区域性。</span><span class="sxs-lookup"><span data-stu-id="392cf-593">Treat values as invariant culture.</span></span>
* <span data-ttu-id="392cf-594">URL 的区域性应固定。</span><span class="sxs-lookup"><span data-stu-id="392cf-594">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="392cf-595">相反，来自窗体数据的值要进行区分区域性的转换。</span><span class="sxs-lookup"><span data-stu-id="392cf-595">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="392cf-596">这是设计使然，目的是让 URL 可在各个区域设置中共享。</span><span class="sxs-lookup"><span data-stu-id="392cf-596">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="392cf-597">使 ASP.NET Core 路由值提供程序和查询字符串值提供程序进行区分区域性的转换：</span><span class="sxs-lookup"><span data-stu-id="392cf-597">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="392cf-598">继承自 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="392cf-598">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="392cf-599">从 [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) 或 [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) 复制代码</span><span class="sxs-lookup"><span data-stu-id="392cf-599">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="392cf-600">使用 [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) 替换传递给值提供程序构造函数的[区域性值](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)</span><span class="sxs-lookup"><span data-stu-id="392cf-600">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="392cf-601">将 MVC 选项中的默认值提供程序工厂替换为新的工厂：</span><span class="sxs-lookup"><span data-stu-id="392cf-601">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="392cf-602">特殊数据类型</span><span class="sxs-lookup"><span data-stu-id="392cf-602">Special data types</span></span>

<span data-ttu-id="392cf-603">模型绑定可以处理某些特殊的数据类型。</span><span class="sxs-lookup"><span data-stu-id="392cf-603">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="392cf-604">IFormFile 和 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="392cf-604">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="392cf-605">HTTP 请求中包含的上传文件。</span><span class="sxs-lookup"><span data-stu-id="392cf-605">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="392cf-606">还支持多个文件的 `IEnumerable<IFormFile>`。</span><span class="sxs-lookup"><span data-stu-id="392cf-606">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="392cf-607">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="392cf-607">CancellationToken</span></span>

<span data-ttu-id="392cf-608">用于取消异步控制器中的活动。</span><span class="sxs-lookup"><span data-stu-id="392cf-608">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="392cf-609">FormCollection</span><span class="sxs-lookup"><span data-stu-id="392cf-609">FormCollection</span></span>

<span data-ttu-id="392cf-610">用于从发布的表单数据中检索所有的值。</span><span class="sxs-lookup"><span data-stu-id="392cf-610">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="392cf-611">输入格式化程序</span><span class="sxs-lookup"><span data-stu-id="392cf-611">Input formatters</span></span>

<span data-ttu-id="392cf-612">请求正文中的数据可以是 JSON、XML 或其他某种格式。</span><span class="sxs-lookup"><span data-stu-id="392cf-612">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="392cf-613">要分析此数据，模型绑定会使用配置为处理特定内容类型的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-613">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="392cf-614">默认情况下，ASP.NET Core 包括用于处理 JSON 数据的基于 JSON 的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-614">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="392cf-615">可以为其他内容类型添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-615">You can add other formatters for other content types.</span></span>

<span data-ttu-id="392cf-616">ASP.NET Core 基于 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性来选择输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-616">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="392cf-617">如果没有属性，它将使用 [Content-Type 标头](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。</span><span class="sxs-lookup"><span data-stu-id="392cf-617">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="392cf-618">要使用内置 XML 输入格式化程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="392cf-618">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="392cf-619">安装 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="392cf-619">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="392cf-620">在 `Startup.ConfigureServices` 中，调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。</span><span class="sxs-lookup"><span data-stu-id="392cf-620">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="392cf-621">将 `Consumes` 属性应用于应在请求正文中使用 XML 的控制器类或操作方法。</span><span class="sxs-lookup"><span data-stu-id="392cf-621">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="392cf-622">有关更多信息，请参阅 [XML 序列化简介](/dotnet/standard/serialization/introducing-xml-serialization)。</span><span class="sxs-lookup"><span data-stu-id="392cf-622">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="392cf-623">从模型绑定中排除指定类型</span><span class="sxs-lookup"><span data-stu-id="392cf-623">Exclude specified types from model binding</span></span>

<span data-ttu-id="392cf-624">模型绑定和验证系统的行为由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 驱动。</span><span class="sxs-lookup"><span data-stu-id="392cf-624">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="392cf-625">可通过向 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) 添加详细信息提供程序来自定义 `ModelMetadata`。</span><span class="sxs-lookup"><span data-stu-id="392cf-625">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="392cf-626">内置详细信息提供程序可用于禁用指定类型的模型绑定或验证。</span><span class="sxs-lookup"><span data-stu-id="392cf-626">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="392cf-627">要禁用指定类型的所有模型的模型绑定，请在 `Startup.ConfigureServices` 中添加 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="392cf-627">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="392cf-628">例如，禁用对 `System.Version` 类型的所有模型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="392cf-628">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="392cf-629">要禁用指定类型的属性的验证，请在 `Startup.ConfigureServices` 中添加 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="392cf-629">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="392cf-630">例如，禁用对 `System.Guid` 类型的属性的验证：</span><span class="sxs-lookup"><span data-stu-id="392cf-630">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="392cf-631">自定义模型绑定器</span><span class="sxs-lookup"><span data-stu-id="392cf-631">Custom model binders</span></span>

<span data-ttu-id="392cf-632">通过编写自定义模型绑定器，并使用 `[ModelBinder]` 属性为给定目标选择该模型绑定器，可扩展模型绑定。</span><span class="sxs-lookup"><span data-stu-id="392cf-632">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="392cf-633">详细了解[自定义模型绑定](xref:mvc/advanced/custom-model-binding)。</span><span class="sxs-lookup"><span data-stu-id="392cf-633">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="392cf-634">手动模型绑定</span><span class="sxs-lookup"><span data-stu-id="392cf-634">Manual model binding</span></span>

<span data-ttu-id="392cf-635">可以使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法手动调用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="392cf-635">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="392cf-636">`ControllerBase` 和 `PageModel` 类上均定义了此方法。</span><span class="sxs-lookup"><span data-stu-id="392cf-636">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="392cf-637">方法重载允许指定要使用的前缀和值提供程序。</span><span class="sxs-lookup"><span data-stu-id="392cf-637">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="392cf-638">如果模型绑定失败，该方法返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="392cf-638">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="392cf-639">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="392cf-639">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="392cf-640">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="392cf-640">[FromServices] attribute</span></span>

<span data-ttu-id="392cf-641">此属性的名称遵循指定数据源的模型绑定属性的模式。</span><span class="sxs-lookup"><span data-stu-id="392cf-641">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="392cf-642">但这与绑定来自值提供程序的数据无关。</span><span class="sxs-lookup"><span data-stu-id="392cf-642">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="392cf-643">它从[依赖关系注入](xref:fundamentals/dependency-injection)容器中获取类型的实例。</span><span class="sxs-lookup"><span data-stu-id="392cf-643">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="392cf-644">其目的在于，在仅当调用特定方法时需要服务的情况下，提供构造函数注入的替代方法。</span><span class="sxs-lookup"><span data-stu-id="392cf-644">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="392cf-645">其他资源</span><span class="sxs-lookup"><span data-stu-id="392cf-645">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
