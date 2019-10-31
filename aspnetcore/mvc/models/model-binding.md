---
title: ASP.NET Core 中的模型绑定
author: rick-anderson
description: 了解 ASP.NET Core 中模型绑定的工作原理以及如何自定义模型绑定的行为。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: aeb2da7e11df1eab5a17e2ae0a3971420c9383b4
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190592"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="14f0b-103">ASP.NET Core 中的模型绑定</span><span class="sxs-lookup"><span data-stu-id="14f0b-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="14f0b-104">本文解释了模型绑定的定义、模型绑定的工作原理，以及如何自定义模型绑定的行为。</span><span class="sxs-lookup"><span data-stu-id="14f0b-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="14f0b-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="14f0b-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="14f0b-106">什么是模型绑定</span><span class="sxs-lookup"><span data-stu-id="14f0b-106">What is Model binding</span></span>

<span data-ttu-id="14f0b-107">控制器和 Razor Pages 处理来自 HTTP 请求的数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="14f0b-108">例如，路由数据可以提供一个记录键，而发布的表单域可以为模型的属性提供一个值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="14f0b-109">编写代码以检索这些值，并将其从字符串转换为 .NET 类型不仅繁琐，而且还容易出错。</span><span class="sxs-lookup"><span data-stu-id="14f0b-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="14f0b-110">模型绑定会自动化该过程。</span><span class="sxs-lookup"><span data-stu-id="14f0b-110">Model binding automates this process.</span></span> <span data-ttu-id="14f0b-111">模型绑定系统：</span><span class="sxs-lookup"><span data-stu-id="14f0b-111">The model binding system:</span></span>

* <span data-ttu-id="14f0b-112">从各种源（如路由数据、表单域和查询字符串）中检索数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="14f0b-113">将数据提供给方法参数和公共属性中的控制器和 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="14f0b-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="14f0b-114">将字符串数据转换为 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="14f0b-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="14f0b-115">更新复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="14f0b-116">示例</span><span class="sxs-lookup"><span data-stu-id="14f0b-116">Example</span></span>

<span data-ttu-id="14f0b-117">假设有以下操作方法：</span><span class="sxs-lookup"><span data-stu-id="14f0b-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="14f0b-118">并且应用收到一个带有以下 URL 的请求：</span><span class="sxs-lookup"><span data-stu-id="14f0b-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="14f0b-119">在路由系统选择该操作方法之后，模型绑定执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="14f0b-119">Model binding goes though the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="14f0b-120">查找 `GetByID` 的第一个参数，该参数是一个名为 `id` 的整数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="14f0b-121">查找 HTTP 请求中的可用源，并在路由数据中查找 `id` =“2”。</span><span class="sxs-lookup"><span data-stu-id="14f0b-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="14f0b-122">将字符串“2”转换为整数 2。</span><span class="sxs-lookup"><span data-stu-id="14f0b-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="14f0b-123">查找 `GetByID` 的下一个参数，该参数是一个名为 `dogsOnly` 的布尔值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="14f0b-124">查找源，并在查询字符串中查找“DogsOnly=true”。</span><span class="sxs-lookup"><span data-stu-id="14f0b-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="14f0b-125">名称匹配不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="14f0b-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="14f0b-126">将字符串“true”转换为布尔值 `true`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="14f0b-127">然后，该框架会调用 `GetById` 方法，为 `id` 参数传入 2，并为 `dogsOnly` 参数传入 `true`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="14f0b-128">在前面的示例中，模型绑定目标是简单类型的方法参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="14f0b-129">目标也可以是复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="14f0b-130">成功绑定每个属性后，将对属性进行[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="14f0b-131">有关绑定到模型的数据以及任意绑定或验证错误的记录都存储在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 中。</span><span class="sxs-lookup"><span data-stu-id="14f0b-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="14f0b-132">为查明该过程是否已成功，应用会检查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 标志。</span><span class="sxs-lookup"><span data-stu-id="14f0b-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="14f0b-133">目标</span><span class="sxs-lookup"><span data-stu-id="14f0b-133">Targets</span></span>

<span data-ttu-id="14f0b-134">模型绑定尝试查找以下类型目标的值：</span><span class="sxs-lookup"><span data-stu-id="14f0b-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="14f0b-135">将请求路由到的控制器操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="14f0b-136">将请求路由到的 Razor Pages 处理程序方法的参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="14f0b-137">控制器或 `PageModel` 类的公共属性（若由特性指定）。</span><span class="sxs-lookup"><span data-stu-id="14f0b-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="14f0b-138">[BindProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-138">[BindProperty] attribute</span></span>

<span data-ttu-id="14f0b-139">可应用于控制器或 `PageModel` 类的公共属性，从而使模型绑定以该属性为目标：</span><span class="sxs-lookup"><span data-stu-id="14f0b-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="14f0b-140">[BindProperties] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-140">[BindProperties] attribute</span></span>

<span data-ttu-id="14f0b-141">可在 ASP.NET Core 2.1 及更高版本中获得。</span><span class="sxs-lookup"><span data-stu-id="14f0b-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="14f0b-142">可应用于控制器或 `PageModel` 类，以使模型绑定以该类的所有公共属性为目标：</span><span class="sxs-lookup"><span data-stu-id="14f0b-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="14f0b-143">HTTP GET 请求的模型绑定</span><span class="sxs-lookup"><span data-stu-id="14f0b-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="14f0b-144">默认情况下，不绑定 HTTP GET 请求的属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="14f0b-145">通常，GET 请求只需一个记录 ID 参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="14f0b-146">记录 ID 用于查找数据库中的项。</span><span class="sxs-lookup"><span data-stu-id="14f0b-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="14f0b-147">因此，无需绑定包含模型实例的属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="14f0b-148">在需要将属性绑定到 GET 请求中的数据的情况下，请将 `SupportsGet` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="14f0b-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="14f0b-149">源</span><span class="sxs-lookup"><span data-stu-id="14f0b-149">Sources</span></span>

<span data-ttu-id="14f0b-150">默认情况下，模型绑定以键值对的形式从 HTTP 请求中的以下源中获取数据：</span><span class="sxs-lookup"><span data-stu-id="14f0b-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="14f0b-151">表单域</span><span class="sxs-lookup"><span data-stu-id="14f0b-151">Form fields</span></span> 
1. <span data-ttu-id="14f0b-152">请求正文（对于[具有 [ApiController] 属性的控制器](xref:web-api/index#binding-source-parameter-inference)。）</span><span class="sxs-lookup"><span data-stu-id="14f0b-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="14f0b-153">路由数据</span><span class="sxs-lookup"><span data-stu-id="14f0b-153">Route data</span></span>
1. <span data-ttu-id="14f0b-154">查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="14f0b-154">Query string parameters</span></span>
1. <span data-ttu-id="14f0b-155">上传的文件</span><span class="sxs-lookup"><span data-stu-id="14f0b-155">Uploaded files</span></span> 

<span data-ttu-id="14f0b-156">对于每个目标参数或属性，将按此列表中指示的顺序扫描源。</span><span class="sxs-lookup"><span data-stu-id="14f0b-156">For each target parameter or property, the sources are scanned in the order indicated in this list.</span></span> <span data-ttu-id="14f0b-157">有几个例外情况：</span><span class="sxs-lookup"><span data-stu-id="14f0b-157">There are a few exceptions:</span></span>

* <span data-ttu-id="14f0b-158">路由数据和查询字符串值仅用于简单类型。</span><span class="sxs-lookup"><span data-stu-id="14f0b-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="14f0b-159">上传的文件仅绑定到实现 `IFormFile` 或 `IEnumerable<IFormFile>` 的目标类型。</span><span class="sxs-lookup"><span data-stu-id="14f0b-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="14f0b-160">如果默认行为没有给出正确结果，则可以使用以下某种属性来指定用于任意给定目标的源。</span><span class="sxs-lookup"><span data-stu-id="14f0b-160">If the default behavior doesn't give the right results, you can use one of the following attributes to specify the source to use for any given target.</span></span> 

* <span data-ttu-id="14f0b-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - 从查询字符串中获取值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-161">[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="14f0b-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - 从路由数据中获取值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-162">[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="14f0b-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - 从发布的表单域中获取值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-163">[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="14f0b-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - 从请求正文中获取值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-164">[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="14f0b-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - 从 HTTP 标头中获取值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-165">[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="14f0b-166">这些属性：</span><span class="sxs-lookup"><span data-stu-id="14f0b-166">These attributes:</span></span>

* <span data-ttu-id="14f0b-167">分别添加到模型属性（而不是模型类），如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="14f0b-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="14f0b-168">选择性地在构造函数中接受模型名称值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="14f0b-169">提供此选项的目的是应对属性名称与请求中的值不匹配的情况。</span><span class="sxs-lookup"><span data-stu-id="14f0b-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="14f0b-170">例如，请求中的值可能是名称中带有连字符的标头，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="14f0b-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="14f0b-171">[FromBody] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-171">[FromBody] attribute</span></span>

<span data-ttu-id="14f0b-172">使用特定于请求内容类型的输入格式化程序来分析请求正文数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-172">The request body data is parsed by using input formatters specific to the content type of the request.</span></span> <span data-ttu-id="14f0b-173">输入格式化程序的解释位于[本文后面部分](#input-formatters)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-173">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="14f0b-174">不要将 `[FromBody]` 应用于每个操作方法的多个参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-174">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="14f0b-175">ASP.NET Core 运行时将读取请求流的责任委托给输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="14f0b-175">The ASP.NET Core runtime delegates the responsibility of reading the request stream to the input formatter.</span></span> <span data-ttu-id="14f0b-176">读取请求流后，无法再次读取该请求来绑定其他 `[FromBody]` 参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-176">Once the request stream is read, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="14f0b-177">其他源</span><span class="sxs-lookup"><span data-stu-id="14f0b-177">Additional sources</span></span>

<span data-ttu-id="14f0b-178">源数据由“值提供程序”提供给模型绑定系统  。</span><span class="sxs-lookup"><span data-stu-id="14f0b-178">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="14f0b-179">你可以编写并注册自定义值提供程序，这些提供程序从其他源中获取用于模型绑定的数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-179">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="14f0b-180">例如，你可能需要来自 Cookie 或会话状态的数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-180">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="14f0b-181">要从新的源中获取数据，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14f0b-181">To get data from a new source:</span></span>

* <span data-ttu-id="14f0b-182">创建用于实现 `IValueProvider` 的类。</span><span class="sxs-lookup"><span data-stu-id="14f0b-182">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="14f0b-183">创建用于实现 `IValueProviderFactory` 的类。</span><span class="sxs-lookup"><span data-stu-id="14f0b-183">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="14f0b-184">在 `Startup.ConfigureServices` 中注册工厂类。</span><span class="sxs-lookup"><span data-stu-id="14f0b-184">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="14f0b-185">示例应用包括从 Cookie 中获取值的 [值提供程序](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs)和[工厂](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs)示例。</span><span class="sxs-lookup"><span data-stu-id="14f0b-185">The sample app includes a [value provider](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) and [factory](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="14f0b-186">以下是 `Startup.ConfigureServices` 中的注册代码：</span><span class="sxs-lookup"><span data-stu-id="14f0b-186">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="14f0b-187">所示代码将自定义值提供程序置于所有内置值提供程序之后。</span><span class="sxs-lookup"><span data-stu-id="14f0b-187">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="14f0b-188">要将其置于列表中的首位，请调用 `Insert(0, new CookieValueProviderFactory())` 而不是 `Add`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-188">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="14f0b-189">不存在模型属性的源</span><span class="sxs-lookup"><span data-stu-id="14f0b-189">No source for a model property</span></span>

<span data-ttu-id="14f0b-190">默认情况下，如果找不到模型属性的值，则不会创建模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="14f0b-190">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="14f0b-191">该属性设置为 NULL 或默认值：</span><span class="sxs-lookup"><span data-stu-id="14f0b-191">The property is set to null or a default value:</span></span>

* <span data-ttu-id="14f0b-192">可以为 Null 的简单类型设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-192">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="14f0b-193">不可以为 Null 的值类型设置为 `default(T)`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-193">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="14f0b-194">例如，参数 `int id` 设置为 0。</span><span class="sxs-lookup"><span data-stu-id="14f0b-194">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="14f0b-195">对于复杂类型，模型绑定使用默认构造函数来创建实例，而不设置属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-195">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="14f0b-196">数组设置为 `Array.Empty<T>()`，但 `byte[]` 数组设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-196">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="14f0b-197">如果在模型属性的表单域中找不到任何内容时，模型状态应无效，请使用 [[BindRequired] 属性](#bindrequired-attribute)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-197">If model state should be invalidated when nothing is found in form fields for a model property, use the [[BindRequired] attribute](#bindrequired-attribute).</span></span>

<span data-ttu-id="14f0b-198">请注意，此 `[BindRequired]` 行为适用于发布的表单数据中的模型绑定，而不适用于请求正文中的 JSON 或 XML 数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-198">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="14f0b-199">请求正文数据由[输入格式化程序](#input-formatters)进行处理。</span><span class="sxs-lookup"><span data-stu-id="14f0b-199">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="14f0b-200">类型转换错误</span><span class="sxs-lookup"><span data-stu-id="14f0b-200">Type conversion errors</span></span>

<span data-ttu-id="14f0b-201">如果找到源，但无法将其转换为目标类型，则模型状态将被标记为无效。</span><span class="sxs-lookup"><span data-stu-id="14f0b-201">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="14f0b-202">目标参数或属性设置为 NULL 或默认值，如上一部分所述。</span><span class="sxs-lookup"><span data-stu-id="14f0b-202">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="14f0b-203">在具有 `[ApiController]` 属性的 API 控制器中，无效的模型状态会导致自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="14f0b-203">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="14f0b-204">在 Razor Pages 中，重新显示带有错误消息的页面：</span><span class="sxs-lookup"><span data-stu-id="14f0b-204">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="14f0b-205">客户端验证会捕获原本会提交到 Razor Pages 表单中的大多数错误数据。</span><span class="sxs-lookup"><span data-stu-id="14f0b-205">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="14f0b-206">此验证使得先前突出显示的代码难以被触发。</span><span class="sxs-lookup"><span data-stu-id="14f0b-206">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="14f0b-207">示例应用包含一个“提交无效日期”按钮，该按钮将错误数据置于“雇用日期”字段中并提交表单   。</span><span class="sxs-lookup"><span data-stu-id="14f0b-207">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="14f0b-208">此按钮显示在发生数据转换错误时用于重新显示页的代码将如何工作。</span><span class="sxs-lookup"><span data-stu-id="14f0b-208">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="14f0b-209">在使用先前的代码重新显示页时，表单域中不会显示无效的输入。</span><span class="sxs-lookup"><span data-stu-id="14f0b-209">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="14f0b-210">这是因为模型属性已设置为 NULL 或默认值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-210">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="14f0b-211">无效输入会出现在错误消息中。</span><span class="sxs-lookup"><span data-stu-id="14f0b-211">The invalid input does appear in an error message.</span></span> <span data-ttu-id="14f0b-212">但是，如果要在表单域中重新显示错误数据，可以考虑将模型属性设置为字符串并手动执行数据转换。</span><span class="sxs-lookup"><span data-stu-id="14f0b-212">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="14f0b-213">如果不希望发生类型转换错误导致模型状态错误的情况，建议使用相同的策略。</span><span class="sxs-lookup"><span data-stu-id="14f0b-213">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="14f0b-214">在这种情况下，将模型属性设置为字符串。</span><span class="sxs-lookup"><span data-stu-id="14f0b-214">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="14f0b-215">简单类型</span><span class="sxs-lookup"><span data-stu-id="14f0b-215">Simple types</span></span>

<span data-ttu-id="14f0b-216">模型绑定器可以将源字符串转换为以下简单类型：</span><span class="sxs-lookup"><span data-stu-id="14f0b-216">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="14f0b-217">布尔值</span><span class="sxs-lookup"><span data-stu-id="14f0b-217">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="14f0b-218">[字节](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="14f0b-218">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="14f0b-219">Char</span><span class="sxs-lookup"><span data-stu-id="14f0b-219">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="14f0b-220">DateTime</span><span class="sxs-lookup"><span data-stu-id="14f0b-220">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="14f0b-221">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="14f0b-221">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="14f0b-222">小数</span><span class="sxs-lookup"><span data-stu-id="14f0b-222">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="14f0b-223">双精度</span><span class="sxs-lookup"><span data-stu-id="14f0b-223">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="14f0b-224">Enum</span><span class="sxs-lookup"><span data-stu-id="14f0b-224">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="14f0b-225">Guid</span><span class="sxs-lookup"><span data-stu-id="14f0b-225">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="14f0b-226">[Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="14f0b-226">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="14f0b-227">单精度</span><span class="sxs-lookup"><span data-stu-id="14f0b-227">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="14f0b-228">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="14f0b-228">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="14f0b-229">[UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="14f0b-229">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="14f0b-230">URI</span><span class="sxs-lookup"><span data-stu-id="14f0b-230">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="14f0b-231">Version</span><span class="sxs-lookup"><span data-stu-id="14f0b-231">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="14f0b-232">复杂类型</span><span class="sxs-lookup"><span data-stu-id="14f0b-232">Complex types</span></span>

<span data-ttu-id="14f0b-233">复杂类型必须具有要绑定的公共默认构造函数和公共可写属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-233">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="14f0b-234">进行模型绑定时，将使用公共默认构造函数来实例化类。</span><span class="sxs-lookup"><span data-stu-id="14f0b-234">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="14f0b-235">对于复杂类型的每个属性，模型绑定会查找名称模式 prefix.property_name 的源  。</span><span class="sxs-lookup"><span data-stu-id="14f0b-235">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="14f0b-236">如果未找到，它将仅查找不含前缀的 properties_name  。</span><span class="sxs-lookup"><span data-stu-id="14f0b-236">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="14f0b-237">对于绑定到参数，前缀是参数名称。</span><span class="sxs-lookup"><span data-stu-id="14f0b-237">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="14f0b-238">对于绑定到 `PageModel` 公共属性，前缀是公共属性名称。</span><span class="sxs-lookup"><span data-stu-id="14f0b-238">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="14f0b-239">某些属性具有 `Prefix` 属性，让你可以替代参数或属性名称的默认用法。</span><span class="sxs-lookup"><span data-stu-id="14f0b-239">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="14f0b-240">例如，假设复杂类型是以下 `Instructor` 类：</span><span class="sxs-lookup"><span data-stu-id="14f0b-240">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="14f0b-241">前缀 = 参数名称</span><span class="sxs-lookup"><span data-stu-id="14f0b-241">Prefix = parameter name</span></span>

<span data-ttu-id="14f0b-242">如果要绑定的模型是一个名为 `instructorToUpdate` 的参数：</span><span class="sxs-lookup"><span data-stu-id="14f0b-242">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="14f0b-243">模型绑定从查找键 `instructorToUpdate.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="14f0b-243">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="14f0b-244">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-244">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="14f0b-245">前缀 = 属性名称</span><span class="sxs-lookup"><span data-stu-id="14f0b-245">Prefix = property name</span></span>

<span data-ttu-id="14f0b-246">如果要绑定的模型是控制器或 `PageModel` 类的一个名为 `Instructor` 的属性：</span><span class="sxs-lookup"><span data-stu-id="14f0b-246">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="14f0b-247">模型绑定从查找键 `Instructor.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="14f0b-247">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="14f0b-248">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-248">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="14f0b-249">自定义前缀</span><span class="sxs-lookup"><span data-stu-id="14f0b-249">Custom prefix</span></span>

<span data-ttu-id="14f0b-250">如果要绑定的模型是名为 `instructorToUpdate` 的参数，并且 `Bind` 属性指定 `Instructor` 作为前缀：</span><span class="sxs-lookup"><span data-stu-id="14f0b-250">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="14f0b-251">模型绑定从查找键 `Instructor.ID` 的源开始操作。</span><span class="sxs-lookup"><span data-stu-id="14f0b-251">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="14f0b-252">如果未找到，它将查找不含前缀的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="14f0b-253">复杂类型目标的属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-253">Attributes for complex type targets</span></span>

<span data-ttu-id="14f0b-254">多个内置属性可用于控制复杂类型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="14f0b-254">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="14f0b-255">如果发布的表单数据是值的源，则这些属性会影响模型绑定。</span><span class="sxs-lookup"><span data-stu-id="14f0b-255">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="14f0b-256">它们不会影响处理发布的 JSON 和 XML 请求正文的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="14f0b-256">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="14f0b-257">输入格式化程序的解释位于[本文后面部分](#input-formatters)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-257">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="14f0b-258">另请参阅[模型验证](xref:mvc/models/validation#required-attribute)中针对 `[Required]` 属性的讨论。</span><span class="sxs-lookup"><span data-stu-id="14f0b-258">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="14f0b-259">[BindRequired] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-259">[BindRequired] attribute</span></span>

<span data-ttu-id="14f0b-260">只能应用于模型属性，不能应用于方法参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-260">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="14f0b-261">如果无法对模型属性进行绑定，则会导致模型绑定添加模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="14f0b-261">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="14f0b-262">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="14f0b-262">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="14f0b-263">[BindNever] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-263">[BindNever] attribute</span></span>

<span data-ttu-id="14f0b-264">只能应用于模型属性，不能应用于方法参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-264">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="14f0b-265">防止模型绑定设置模型的属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-265">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="14f0b-266">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="14f0b-266">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="14f0b-267">[Bind] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-267">[Bind] attribute</span></span>

<span data-ttu-id="14f0b-268">可应用于类或方法参数。</span><span class="sxs-lookup"><span data-stu-id="14f0b-268">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="14f0b-269">指定模型绑定中应包含的模型属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-269">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="14f0b-270">在下面的示例中，当调用任意处理程序或操作方法时，只绑定 `Instructor` 模型的指定属性：</span><span class="sxs-lookup"><span data-stu-id="14f0b-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="14f0b-271">在下面的示例中，当调用 `OnPost` 方法时，只绑定 `Instructor` 模型的指定属性：</span><span class="sxs-lookup"><span data-stu-id="14f0b-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="14f0b-272">`[Bind]` 属性可用于防止“创建”方案中的过多发布情况  。</span><span class="sxs-lookup"><span data-stu-id="14f0b-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="14f0b-273">由于排除的属性设置为 NULL 或默认值，而不是保持不变，因此它在编辑方案中无法很好地工作。</span><span class="sxs-lookup"><span data-stu-id="14f0b-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="14f0b-274">为防止过多发布，建议使用视图模型，而不是使用 `[Bind]` 属性。</span><span class="sxs-lookup"><span data-stu-id="14f0b-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="14f0b-275">有关详细信息，请参阅[有关过多发布的安全性说明](xref:data/ef-mvc/crud#security-note-about-overposting)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="14f0b-276">集合</span><span class="sxs-lookup"><span data-stu-id="14f0b-276">Collections</span></span>

<span data-ttu-id="14f0b-277">对于是简单类型集合的目标，模型绑定将查找 parameter_name 或 property_name 的匹配项   。</span><span class="sxs-lookup"><span data-stu-id="14f0b-277">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="14f0b-278">如果找不到匹配项，它将查找某种不含前缀的受支持的格式。</span><span class="sxs-lookup"><span data-stu-id="14f0b-278">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="14f0b-279">例如:</span><span class="sxs-lookup"><span data-stu-id="14f0b-279">For example:</span></span>

* <span data-ttu-id="14f0b-280">假设要绑定的参数是名为 `selectedCourses` 的数组：</span><span class="sxs-lookup"><span data-stu-id="14f0b-280">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="14f0b-281">表单或查询字符串数据可以采用以下某种格式：</span><span class="sxs-lookup"><span data-stu-id="14f0b-281">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="14f0b-282">只有表单数据支持以下格式：</span><span class="sxs-lookup"><span data-stu-id="14f0b-282">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="14f0b-283">对于前面所有的示例格式，模型绑定将两个项的数组传递给 `selectedCourses` 参数：</span><span class="sxs-lookup"><span data-stu-id="14f0b-283">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="14f0b-284">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="14f0b-284">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="14f0b-285">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="14f0b-285">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="14f0b-286">使用下标数字的数据格式 (... [0] ... [1] ...) 必须确保从零开始按顺序进行编号。</span><span class="sxs-lookup"><span data-stu-id="14f0b-286">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="14f0b-287">如果下标编号中存在任何间隔，则间隔后的所有项都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="14f0b-287">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="14f0b-288">例如，如果下标是 0 和 2，而不是 0 和 1，则第二个项会被忽略。</span><span class="sxs-lookup"><span data-stu-id="14f0b-288">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="14f0b-289">字典</span><span class="sxs-lookup"><span data-stu-id="14f0b-289">Dictionaries</span></span>

<span data-ttu-id="14f0b-290">对于 `Dictionary` 目标，模型绑定会查找 parameter_name 或 property_name 的匹配项   。</span><span class="sxs-lookup"><span data-stu-id="14f0b-290">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="14f0b-291">如果找不到匹配项，它将查找某种不含前缀的受支持的格式。</span><span class="sxs-lookup"><span data-stu-id="14f0b-291">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="14f0b-292">例如:</span><span class="sxs-lookup"><span data-stu-id="14f0b-292">For example:</span></span>

* <span data-ttu-id="14f0b-293">假设目标参数是名为 `selectedCourses` 的 `Dictionary<int, string>`：</span><span class="sxs-lookup"><span data-stu-id="14f0b-293">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="14f0b-294">发布的表单或查询字符串数据可以类似于以下某一示例：</span><span class="sxs-lookup"><span data-stu-id="14f0b-294">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="14f0b-295">对于前面所有的示例格式，模型绑定将两个项的字典传递给 `selectedCourses` 参数：</span><span class="sxs-lookup"><span data-stu-id="14f0b-295">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="14f0b-296">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="14f0b-296">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="14f0b-297">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="14f0b-297">selectedCourses["2000"]="Economics"</span></span>

## <a name="special-data-types"></a><span data-ttu-id="14f0b-298">特殊数据类型</span><span class="sxs-lookup"><span data-stu-id="14f0b-298">Special data types</span></span>

<span data-ttu-id="14f0b-299">模型绑定可以处理某些特殊的数据类型。</span><span class="sxs-lookup"><span data-stu-id="14f0b-299">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="14f0b-300">IFormFile 和 IFormFileCollection</span><span class="sxs-lookup"><span data-stu-id="14f0b-300">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="14f0b-301">HTTP 请求中包含的上传文件。</span><span class="sxs-lookup"><span data-stu-id="14f0b-301">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="14f0b-302">还支持多个文件的 `IEnumerable<IFormFile>`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-302">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="14f0b-303">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="14f0b-303">CancellationToken</span></span>

<span data-ttu-id="14f0b-304">用于取消异步控制器中的活动。</span><span class="sxs-lookup"><span data-stu-id="14f0b-304">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="14f0b-305">FormCollection</span><span class="sxs-lookup"><span data-stu-id="14f0b-305">FormCollection</span></span>

<span data-ttu-id="14f0b-306">用于从发布的表单数据中检索所有的值。</span><span class="sxs-lookup"><span data-stu-id="14f0b-306">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="14f0b-307">输入格式化程序</span><span class="sxs-lookup"><span data-stu-id="14f0b-307">Input formatters</span></span>

<span data-ttu-id="14f0b-308">请求正文中的数据可以是 JSON、XML 或其他某种格式。</span><span class="sxs-lookup"><span data-stu-id="14f0b-308">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="14f0b-309">要分析此数据，模型绑定会使用配置为处理特定内容类型的输入格式化程序  。</span><span class="sxs-lookup"><span data-stu-id="14f0b-309">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="14f0b-310">默认情况下，ASP.NET Core 包括用于处理 JSON 数据的基于 JSON 的输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="14f0b-310">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="14f0b-311">可以为其他内容类型添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="14f0b-311">You can add other formatters for other content types.</span></span>

<span data-ttu-id="14f0b-312">ASP.NET Core 基于 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 属性来选择输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="14f0b-312">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="14f0b-313">如果没有属性，它将使用 [Content-Type 标头](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-313">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="14f0b-314">要使用内置 XML 输入格式化程序，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="14f0b-314">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="14f0b-315">安装 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="14f0b-315">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="14f0b-316">在 `Startup.ConfigureServices` 中，调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。</span><span class="sxs-lookup"><span data-stu-id="14f0b-316">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="14f0b-317">将 `Consumes` 属性应用于应在请求正文中使用 XML 的控制器类或操作方法。</span><span class="sxs-lookup"><span data-stu-id="14f0b-317">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="14f0b-318">有关更多信息，请参阅 [XML 序列化简介](/dotnet/standard/serialization/introducing-xml-serialization)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-318">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="14f0b-319">从模型绑定中排除指定类型</span><span class="sxs-lookup"><span data-stu-id="14f0b-319">Exclude specified types from model binding</span></span>

<span data-ttu-id="14f0b-320">模型绑定和验证系统的行为由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 驱动。</span><span class="sxs-lookup"><span data-stu-id="14f0b-320">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="14f0b-321">可通过向 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) 添加详细信息提供程序来自定义 `ModelMetadata`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-321">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="14f0b-322">内置详细信息提供程序可用于禁用指定类型的模型绑定或验证。</span><span class="sxs-lookup"><span data-stu-id="14f0b-322">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="14f0b-323">要禁用指定类型的所有模型的模型绑定，请在 `Startup.ConfigureServices` 中添加 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="14f0b-323">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14f0b-324">例如，禁用对 `System.Version` 类型的所有模型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="14f0b-324">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="14f0b-325">要禁用指定类型的属性的验证，请在 `Startup.ConfigureServices` 中添加 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。</span><span class="sxs-lookup"><span data-stu-id="14f0b-325">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14f0b-326">例如，禁用对 `System.Guid` 类型的属性的验证：</span><span class="sxs-lookup"><span data-stu-id="14f0b-326">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="14f0b-327">自定义模型绑定器</span><span class="sxs-lookup"><span data-stu-id="14f0b-327">Custom model binders</span></span>

<span data-ttu-id="14f0b-328">通过编写自定义模型绑定器，并使用 `[ModelBinder]` 属性为给定目标选择该模型绑定器，可扩展模型绑定。</span><span class="sxs-lookup"><span data-stu-id="14f0b-328">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="14f0b-329">详细了解[自定义模型绑定](xref:mvc/advanced/custom-model-binding)。</span><span class="sxs-lookup"><span data-stu-id="14f0b-329">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="14f0b-330">手动模型绑定</span><span class="sxs-lookup"><span data-stu-id="14f0b-330">Manual model binding</span></span>

<span data-ttu-id="14f0b-331">可以使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法手动调用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="14f0b-331">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="14f0b-332">`ControllerBase` 和 `PageModel` 类上均定义了此方法。</span><span class="sxs-lookup"><span data-stu-id="14f0b-332">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="14f0b-333">方法重载允许指定要使用的前缀和值提供程序。</span><span class="sxs-lookup"><span data-stu-id="14f0b-333">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="14f0b-334">如果模型绑定失败，该方法返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="14f0b-334">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="14f0b-335">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="14f0b-335">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="14f0b-336">[FromServices] 属性</span><span class="sxs-lookup"><span data-stu-id="14f0b-336">[FromServices] attribute</span></span>

<span data-ttu-id="14f0b-337">此属性的名称遵循指定数据源的模型绑定属性的模式。</span><span class="sxs-lookup"><span data-stu-id="14f0b-337">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="14f0b-338">但这与绑定来自值提供程序的数据无关。</span><span class="sxs-lookup"><span data-stu-id="14f0b-338">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="14f0b-339">它从[依赖关系注入](xref:fundamentals/dependency-injection)容器中获取类型的实例。</span><span class="sxs-lookup"><span data-stu-id="14f0b-339">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="14f0b-340">其目的在于，在仅当调用特定方法时需要服务的情况下，提供构造函数注入的替代方法。</span><span class="sxs-lookup"><span data-stu-id="14f0b-340">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14f0b-341">其他资源</span><span class="sxs-lookup"><span data-stu-id="14f0b-341">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
