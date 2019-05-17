---
title: ASP.NET Core 中的模型绑定
author: tdykstra
description: 了解 ASP.NET Core MVC 中的模型绑定如何将 HTTP 请求中的数据映射到操作方法参数。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 11/13/2018
uid: mvc/models/model-binding
ms.openlocfilehash: 1dc9b41328ed78440622acc1865b6f088d394403
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883142"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="55310-103">ASP.NET Core 中的模型绑定</span><span class="sxs-lookup"><span data-stu-id="55310-103">Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="55310-104">作者：[Rachel Appel](https://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="55310-104">By [Rachel Appel](https://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-binding"></a><span data-ttu-id="55310-105">模型绑定简介</span><span class="sxs-lookup"><span data-stu-id="55310-105">Introduction to model binding</span></span>

<span data-ttu-id="55310-106">ASP.NET Core MVC 中的模型绑定将 HTTP 请求中的数据映射到操作方法参数。</span><span class="sxs-lookup"><span data-stu-id="55310-106">Model binding in ASP.NET Core MVC maps data from HTTP requests to action method parameters.</span></span> <span data-ttu-id="55310-107">这些参数可能是简单类型的参数，如字符串、整数或浮点数，也可能是复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="55310-107">The parameters may be simple types such as strings, integers, or floats, or they may be complex types.</span></span> <span data-ttu-id="55310-108">这是 MVC 的一项强大功能，因为不管数据的大小和复杂性，将传入数据映射到对应位置都是经常重复的方案。</span><span class="sxs-lookup"><span data-stu-id="55310-108">This is a great feature of MVC because mapping incoming data to a counterpart is an often repeated scenario, regardless of size or complexity of the data.</span></span> <span data-ttu-id="55310-109">MVC 通过将绑定抽象出来解决了这一问题，使开发者不必在每个应用中重写同一代码的稍微不同版本。</span><span class="sxs-lookup"><span data-stu-id="55310-109">MVC solves this problem by abstracting binding away so developers don't have to keep rewriting a slightly different version of that same code in every app.</span></span> <span data-ttu-id="55310-110">向类型转换器代码写入自己的文本不仅繁琐乏味，而且容易出错。</span><span class="sxs-lookup"><span data-stu-id="55310-110">Writing your own text to type converter code is tedious, and error prone.</span></span>

## <a name="how-model-binding-works"></a><span data-ttu-id="55310-111">模型绑定的工作原理</span><span class="sxs-lookup"><span data-stu-id="55310-111">How model binding works</span></span>

<span data-ttu-id="55310-112">当 MVC 收到 HTTP 请求时，它会将此请求路由到控制器的特定操作方法。</span><span class="sxs-lookup"><span data-stu-id="55310-112">When MVC receives an HTTP request, it routes it to a specific action method of a controller.</span></span> <span data-ttu-id="55310-113">它基于路由数据中的内容决定要运行的操作方法，然后将 HTTP 请求中的值绑定到该操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="55310-113">It determines which action method to run based on what is in the route data, then it binds values from the HTTP request to that action method's parameters.</span></span> <span data-ttu-id="55310-114">以下列 URL 为例：</span><span class="sxs-lookup"><span data-stu-id="55310-114">For example, consider the following URL:</span></span>

`http://contoso.com/movies/edit/2`

<span data-ttu-id="55310-115">由于路由模板为 `{controller=Home}/{action=Index}/{id?}`，因此，`movies/edit/2` 将路由到 `Movies` 控制器及其 `Edit` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="55310-115">Since the route template looks like this, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` routes to the `Movies` controller, and its `Edit` action method.</span></span> <span data-ttu-id="55310-116">此外，它还接受名为 `id` 的可选参数。</span><span class="sxs-lookup"><span data-stu-id="55310-116">It also accepts an optional parameter called `id`.</span></span> <span data-ttu-id="55310-117">操作方法的代码应如下所示：</span><span class="sxs-lookup"><span data-stu-id="55310-117">The code for the action method should look something like this:</span></span>

```csharp
public IActionResult Edit(int? id)
   ```

<span data-ttu-id="55310-118">注意:URL 路由中的字符串不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="55310-118">Note: The strings in the URL route are not case sensitive.</span></span>

<span data-ttu-id="55310-119">MVC 将尝试按名称将请求数据绑定到操作参数。</span><span class="sxs-lookup"><span data-stu-id="55310-119">MVC will try to bind request data to the action parameters by name.</span></span> <span data-ttu-id="55310-120">MVC 将使用参数名称和其公共可设置属性的名称查找每个参数的值。</span><span class="sxs-lookup"><span data-stu-id="55310-120">MVC will look for values for each parameter using the parameter name and the names of its public settable properties.</span></span> <span data-ttu-id="55310-121">在以上示例中，唯一的操作参数名为 `id`，MVC 会将此参数绑定到路由值中具有相同名称的值。</span><span class="sxs-lookup"><span data-stu-id="55310-121">In the above example, the only action parameter is named `id`, which MVC binds to the value with the same name in the route values.</span></span> <span data-ttu-id="55310-122">除路由值外，MVC 还会绑定来自请求各个部分的数据，并按一定顺序执行此操作。</span><span class="sxs-lookup"><span data-stu-id="55310-122">In addition to route values MVC will bind data from various parts of the request and it does so in a set order.</span></span> <span data-ttu-id="55310-123">下面是一个数据源列表（按模型绑定查看的顺序排列）：</span><span class="sxs-lookup"><span data-stu-id="55310-123">Below is a list of the data sources in the order that model binding looks through them:</span></span>

1. <span data-ttu-id="55310-124">`Form values`：这些是使用 POST 方法进入 HTTP 请求的窗体值。</span><span class="sxs-lookup"><span data-stu-id="55310-124">`Form values`: These are form values that go in the HTTP request using the POST method.</span></span> <span data-ttu-id="55310-125">（包括 jQuery POST 请求）。</span><span class="sxs-lookup"><span data-stu-id="55310-125">(including jQuery POST requests).</span></span>

2. <span data-ttu-id="55310-126">`Route values`：[路由](xref:fundamentals/routing)提供的路由值集</span><span class="sxs-lookup"><span data-stu-id="55310-126">`Route values`: The set of route values provided by [Routing](xref:fundamentals/routing)</span></span>

3. <span data-ttu-id="55310-127">`Query strings`：URI 的查询字符串部分。</span><span class="sxs-lookup"><span data-stu-id="55310-127">`Query strings`: The query string part of the URI.</span></span>

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

<span data-ttu-id="55310-128">注意:窗体值、路由数据和查询字符串均存储为名称/值对。</span><span class="sxs-lookup"><span data-stu-id="55310-128">Note: Form values, route data, and query strings are all stored as name-value pairs.</span></span>

<span data-ttu-id="55310-129">由于模型绑定请求名为 `id` 的键，而窗体值中没有任何名为 `id` 的键，因此，它将转到路由值以查找该键。</span><span class="sxs-lookup"><span data-stu-id="55310-129">Since model binding asked for a key named `id` and there's nothing named `id` in the form values, it moved on to the route values looking for that key.</span></span> <span data-ttu-id="55310-130">在本示例中，该键是一个匹配项。</span><span class="sxs-lookup"><span data-stu-id="55310-130">In our example, it's a match.</span></span> <span data-ttu-id="55310-131">发生绑定，并且值被转换为整数 2。</span><span class="sxs-lookup"><span data-stu-id="55310-131">Binding happens, and the value is converted to the integer 2.</span></span> <span data-ttu-id="55310-132">使用 Edit(string id) 的同一请求将转换为字符串“2”。</span><span class="sxs-lookup"><span data-stu-id="55310-132">The same request using Edit(string id) would convert to the string "2".</span></span>

<span data-ttu-id="55310-133">到目前为止，示例使用的都是简单类型。</span><span class="sxs-lookup"><span data-stu-id="55310-133">So far the example uses simple types.</span></span> <span data-ttu-id="55310-134">在 MVC 中，简单类型是任何 .NET 基元类型或包含字符串类型转换器的类型。</span><span class="sxs-lookup"><span data-stu-id="55310-134">In MVC simple types are any .NET primitive type or type with a string type converter.</span></span> <span data-ttu-id="55310-135">如果操作方法的参数是一个属性为简单类型和复杂类型的类，如 `Movie` 类型，则 MVC 的模型绑定仍可对其进行妥善处理。</span><span class="sxs-lookup"><span data-stu-id="55310-135">If the action method's parameter were a class such as the `Movie` type, which contains both simple and complex types as properties, MVC's model binding will still handle it nicely.</span></span> <span data-ttu-id="55310-136">它使用反射和递归来遍历查找匹配项的复杂类型的属性。</span><span class="sxs-lookup"><span data-stu-id="55310-136">It uses reflection and recursion to traverse the properties of complex types looking for matches.</span></span> <span data-ttu-id="55310-137">模型绑定查找模式 parameter_name.property_name 以将值绑定到属性 。</span><span class="sxs-lookup"><span data-stu-id="55310-137">Model binding looks for the pattern *parameter_name.property_name* to bind values to properties.</span></span> <span data-ttu-id="55310-138">如果未找到此窗体的匹配值，它将尝试仅使用属性名称进行绑定。</span><span class="sxs-lookup"><span data-stu-id="55310-138">If it doesn't find matching values of this form, it will attempt to bind using just the property name.</span></span> <span data-ttu-id="55310-139">对于诸如 `Collection` 类型的类型，模型绑定将查找 parameter_name[index] 或仅 [index] 的匹配项。</span><span class="sxs-lookup"><span data-stu-id="55310-139">For those types such as `Collection` types, model binding looks for matches to *parameter_name[index]* or just *[index]*.</span></span> <span data-ttu-id="55310-140">对于 `Dictionary` 类型，模型绑定的处理方法与之类似，即请求 parameter_name[key] 或仅 [key]（只要键是简单类型）。</span><span class="sxs-lookup"><span data-stu-id="55310-140">Model binding treats  `Dictionary` types similarly, asking for *parameter_name[key]* or just *[key]*, as long as the keys are simple types.</span></span> <span data-ttu-id="55310-141">受支持的键匹配字段名称 HTML 和针对相同模型类型生成的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="55310-141">Keys that are supported match the field names HTML and tag helpers generated for the same model type.</span></span> <span data-ttu-id="55310-142">这样就可以启用往返值，以便用户仍能方便地输入内容来填充窗体字段，例如当创建或编辑的绑定数据未通过验证时。</span><span class="sxs-lookup"><span data-stu-id="55310-142">This enables round-tripping values so that the form fields remain filled with the user's input for their convenience, for example, when bound data from a create or edit didn't pass validation.</span></span>

<span data-ttu-id="55310-143">若要实现模型绑定，该类必须具有要绑定的公共默认构造函数和公共可写属性。</span><span class="sxs-lookup"><span data-stu-id="55310-143">To make model binding possible, the class must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="55310-144">发生模型绑定时，在使用公共默认构造函数对类进行实例化后才可设置属性。</span><span class="sxs-lookup"><span data-stu-id="55310-144">When model binding occurs, the class is instantiated using the public default constructor, then the properties can be set.</span></span>

<span data-ttu-id="55310-145">绑定参数时，模型绑定将停止查找具有该名称的值并向前移动以绑定下一个参数。</span><span class="sxs-lookup"><span data-stu-id="55310-145">When a parameter is bound, model binding stops looking for values with that name and it moves on to bind the next parameter.</span></span> <span data-ttu-id="55310-146">否则，默认模型绑定行为将根据参数的类型将参数设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="55310-146">Otherwise, the default model binding behavior sets parameters to their default values depending on their type:</span></span>

* <span data-ttu-id="55310-147">`T[]`：除 `byte[]` 类型的数组外，绑定将 `T[]` 类型的参数设置为 `Array.Empty<T>()`。</span><span class="sxs-lookup"><span data-stu-id="55310-147">`T[]`: With the exception of arrays of type `byte[]`, binding sets parameters of type `T[]` to `Array.Empty<T>()`.</span></span> <span data-ttu-id="55310-148">`byte[]` 类型的数组设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="55310-148">Arrays of type `byte[]` are set to `null`.</span></span>

* <span data-ttu-id="55310-149">引用类型：绑定通过默认构造函数创建类的实例，而不设置属性。</span><span class="sxs-lookup"><span data-stu-id="55310-149">Reference Types: Binding creates an instance of a class with the default constructor without setting properties.</span></span> <span data-ttu-id="55310-150">但是，模型绑定将 `string` 参数设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="55310-150">However, model binding sets `string` parameters to `null`.</span></span>

* <span data-ttu-id="55310-151">可以为 null 的类型：可以为 null 的类型设置为 `null`。</span><span class="sxs-lookup"><span data-stu-id="55310-151">Nullable Types: Nullable types are set to `null`.</span></span> <span data-ttu-id="55310-152">在上面的示例中，模型绑定将 `id` 设置为 `null`，因为其类型为 `int?`。</span><span class="sxs-lookup"><span data-stu-id="55310-152">In the above example, model binding sets `id` to `null` since it's of type `int?`.</span></span>

* <span data-ttu-id="55310-153">值类型：`T` 类型的不可以为 null 的值类型设置为 `default(T)`。</span><span class="sxs-lookup"><span data-stu-id="55310-153">Value Types: Non-nullable value types of type `T` are set to `default(T)`.</span></span> <span data-ttu-id="55310-154">例如，模型绑定将参数 `int id` 设置为 0。</span><span class="sxs-lookup"><span data-stu-id="55310-154">For example, model binding will set a parameter `int id` to 0.</span></span> <span data-ttu-id="55310-155">请考虑使用模型验证或可以为 null 的类型，而不是依赖于默认值。</span><span class="sxs-lookup"><span data-stu-id="55310-155">Consider using model validation or nullable types rather than relying on default values.</span></span>

<span data-ttu-id="55310-156">如果绑定失败，MVC 不会引发错误。</span><span class="sxs-lookup"><span data-stu-id="55310-156">If binding fails, MVC doesn't throw an error.</span></span> <span data-ttu-id="55310-157">接受用户输入的每个操作均应检查 `ModelState.IsValid` 属性。</span><span class="sxs-lookup"><span data-stu-id="55310-157">Every action which accepts user input should check the `ModelState.IsValid` property.</span></span>

<span data-ttu-id="55310-158">注意:控制器的 `ModelState` 属性中的每个条目均为包含 `Errors` 属性的 `ModelStateEntry`。</span><span class="sxs-lookup"><span data-stu-id="55310-158">Note: Each entry in the controller's `ModelState` property is a `ModelStateEntry` containing an `Errors` property.</span></span> <span data-ttu-id="55310-159">很少需要自行查询此集合。</span><span class="sxs-lookup"><span data-stu-id="55310-159">It's rarely necessary to query this collection yourself.</span></span> <span data-ttu-id="55310-160">请改用 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="55310-160">Use `ModelState.IsValid` instead.</span></span>

<span data-ttu-id="55310-161">此外，MVC 在执行模型绑定时必须考虑一些特殊数据类型：</span><span class="sxs-lookup"><span data-stu-id="55310-161">Additionally, there are some special data types that MVC must consider when performing model binding:</span></span>

* <span data-ttu-id="55310-162">`IFormFile`、`IEnumerable<IFormFile>`：作为 HTTP 请求一部分的一个或多个已上传文件。</span><span class="sxs-lookup"><span data-stu-id="55310-162">`IFormFile`, `IEnumerable<IFormFile>`: One or more uploaded files that are part of the HTTP request.</span></span>

* <span data-ttu-id="55310-163">`CancellationToken`：用于取消异步控制器中的活动。</span><span class="sxs-lookup"><span data-stu-id="55310-163">`CancellationToken`: Used to cancel activity in asynchronous controllers.</span></span>

<span data-ttu-id="55310-164">这些类型可绑定到操作参数或类类型的属性。</span><span class="sxs-lookup"><span data-stu-id="55310-164">These types can be bound to action parameters or to properties on a class type.</span></span>

<span data-ttu-id="55310-165">模型绑定完成后，将发生[验证](validation.md)。</span><span class="sxs-lookup"><span data-stu-id="55310-165">Once model binding is complete, [Validation](validation.md) occurs.</span></span> <span data-ttu-id="55310-166">对于绝大多数开发方案，默认模型绑定效果极佳。</span><span class="sxs-lookup"><span data-stu-id="55310-166">Default model binding works great for the vast majority of development scenarios.</span></span> <span data-ttu-id="55310-167">它还可以扩展，因此如果你有特殊需求，则可自定义内置行为。</span><span class="sxs-lookup"><span data-stu-id="55310-167">It's also extensible so if you have unique needs you can customize the built-in behavior.</span></span>

## <a name="customize-model-binding-behavior-with-attributes"></a><span data-ttu-id="55310-168">通过特性自定义模型绑定行为</span><span class="sxs-lookup"><span data-stu-id="55310-168">Customize model binding behavior with attributes</span></span>

<span data-ttu-id="55310-169">MVC 包含多种特性，可用于将其默认模型绑定行为定向到不同的源。</span><span class="sxs-lookup"><span data-stu-id="55310-169">MVC contains several attributes that you can use to direct its default model binding behavior to a different source.</span></span> <span data-ttu-id="55310-170">例如，可以使用 `[BindRequired]` 或 `[BindNever]` 特性指定属性是否需要绑定，或绑定是否根本不应发生。</span><span class="sxs-lookup"><span data-stu-id="55310-170">For example, you can specify whether binding is required for a property, or if it should never happen at all by using the `[BindRequired]` or `[BindNever]` attributes.</span></span> <span data-ttu-id="55310-171">或者，可以替代默认数据源，并指定模型绑定器的数据源。</span><span class="sxs-lookup"><span data-stu-id="55310-171">Alternatively, you can override the default data source, and specify the model binder's data source.</span></span> <span data-ttu-id="55310-172">下面是模型绑定特性的列表：</span><span class="sxs-lookup"><span data-stu-id="55310-172">Below is a list of model binding attributes:</span></span>

* <span data-ttu-id="55310-173">`[BindRequired]`：如果无法发生绑定，此特性将添加模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="55310-173">`[BindRequired]`: This attribute adds a model state error if binding cannot occur.</span></span>

* <span data-ttu-id="55310-174">`[BindNever]`：指示模型绑定器从不绑定到此参数。</span><span class="sxs-lookup"><span data-stu-id="55310-174">`[BindNever]`: Tells the model binder to never bind to this parameter.</span></span>

* <span data-ttu-id="55310-175">`[FromHeader]`、`[FromQuery]`、`[FromRoute]`、`[FromForm]`：使用这些特性指定要应用的确切绑定源。</span><span class="sxs-lookup"><span data-stu-id="55310-175">`[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Use these to specify the exact binding source you want to apply.</span></span>

* <span data-ttu-id="55310-176">`[FromServices]`：此特性使用[依赖关系注入](../../fundamentals/dependency-injection.md)绑定服务中的参数。</span><span class="sxs-lookup"><span data-stu-id="55310-176">`[FromServices]`: This attribute uses [dependency injection](../../fundamentals/dependency-injection.md) to bind parameters from services.</span></span>

* <span data-ttu-id="55310-177">`[FromBody]`：使用配置的格式化程序绑定请求正文中的数据。</span><span class="sxs-lookup"><span data-stu-id="55310-177">`[FromBody]`: Use the configured formatters to bind data from the request body.</span></span> <span data-ttu-id="55310-178">基于请求的内容类型，选择格式化程序。</span><span class="sxs-lookup"><span data-stu-id="55310-178">The formatter is selected based on content type of the request.</span></span>

* <span data-ttu-id="55310-179">`[ModelBinder]`：用于替代默认模型绑定器、绑定源和名称。</span><span class="sxs-lookup"><span data-stu-id="55310-179">`[ModelBinder]`: Used to override the default model binder, binding source and name.</span></span>

<span data-ttu-id="55310-180">需要替代模型绑定的默认行为时，特性是非常有用的工具。</span><span class="sxs-lookup"><span data-stu-id="55310-180">Attributes are very helpful tools when you need to override the default behavior of model binding.</span></span>

## <a name="customize-model-binding-and-validation-globally"></a><span data-ttu-id="55310-181">全局自定义模型绑定和验证</span><span class="sxs-lookup"><span data-stu-id="55310-181">Customize model binding and validation globally</span></span>

<span data-ttu-id="55310-182">由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 驱动模型绑定和验证系统的行为，该行为描述：</span><span class="sxs-lookup"><span data-stu-id="55310-182">The model binding and validation system's behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) that describes:</span></span>

* <span data-ttu-id="55310-183">如何绑定模型。</span><span class="sxs-lookup"><span data-stu-id="55310-183">How a model is to be bound.</span></span>
* <span data-ttu-id="55310-184">如何验证类型及其属性。</span><span class="sxs-lookup"><span data-stu-id="55310-184">How validation occurs on the type and its properties.</span></span>

<span data-ttu-id="55310-185">通过向 [MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders) 添加详细信息提供程序，可以全局配置系统行为的各个方面。</span><span class="sxs-lookup"><span data-stu-id="55310-185">Aspects of the system's behavior can be configured globally by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="55310-186">MVC 有一些内置的详细信息提供程序，可通过它们配置禁用模型绑定或验证某些类型等行为。</span><span class="sxs-lookup"><span data-stu-id="55310-186">MVC has a few built-in details providers that allow configuring behavior such as disabling model binding or validation for certain types.</span></span>

<span data-ttu-id="55310-187">若要禁用对特定类型的所有模型的模型绑定，请在 `Startup.ConfigureServices` 中添加 [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider)。</span><span class="sxs-lookup"><span data-stu-id="55310-187">To disable model binding on all models of a certain type, add an [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55310-188">例如，禁用对 `System.Version` 类型的所有模型的模型绑定：</span><span class="sxs-lookup"><span data-stu-id="55310-188">For example, to disable model binding on all models of type `System.Version`:</span></span>

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new ExcludeBindingMetadataProvider(typeof(System.Version))));
```

<span data-ttu-id="55310-189">要禁用对特定类型的属性的验证，请在 `Startup.ConfigureServices` 中添加 [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider)。</span><span class="sxs-lookup"><span data-stu-id="55310-189">To disable validation on properties of a certain type, add a [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="55310-190">例如，禁用对 `System.Guid` 类型的属性的验证：</span><span class="sxs-lookup"><span data-stu-id="55310-190">For example, to disable validation on properties of type `System.Guid`:</span></span>

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new SuppressChildValidationMetadataProvider(typeof(System.Guid))));
```

## <a name="bind-formatted-data-from-the-request-body"></a><span data-ttu-id="55310-191">绑定请求正文中的带格式数据</span><span class="sxs-lookup"><span data-stu-id="55310-191">Bind formatted data from the request body</span></span>

<span data-ttu-id="55310-192">请求数据可以有各种格式，包括 JSON、XML 和许多其他格式。</span><span class="sxs-lookup"><span data-stu-id="55310-192">Request data can come in a variety of formats including JSON, XML and many others.</span></span> <span data-ttu-id="55310-193">使用 [FromBody] 特性指示要将参数绑定到请求正文中的数据时，MVC 会使用一组已配置的格式化程序基于请求数据的内容类型对请求数据进行处理。</span><span class="sxs-lookup"><span data-stu-id="55310-193">When you use the [FromBody] attribute to indicate that you want to bind a parameter to data in the request body, MVC uses a configured set of formatters to handle the request data based on its content type.</span></span> <span data-ttu-id="55310-194">默认情况下，MVC 包括用于处理 JSON 数据的 `JsonInputFormatter` 类，但你可以添加用于处理 XML 和其他自定义格式的其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="55310-194">By default MVC includes a `JsonInputFormatter` class for handling JSON data, but you can add additional formatters for handling XML and other custom formats.</span></span>

> [!NOTE]
> <span data-ttu-id="55310-195">对于用 `[FromBody]` 修饰的每个操作，最多可以有一个参数。</span><span class="sxs-lookup"><span data-stu-id="55310-195">There can be at most one parameter per action decorated with `[FromBody]`.</span></span> <span data-ttu-id="55310-196">ASP.NET Core MVC 运行时向格式化程序委托读取请求流的责任。</span><span class="sxs-lookup"><span data-stu-id="55310-196">The ASP.NET Core MVC run-time delegates the responsibility of reading the request stream to the formatter.</span></span> <span data-ttu-id="55310-197">读取参数的请求流后，通常不能为绑定其他 `[FromBody]` 参数而再次读取请求流。</span><span class="sxs-lookup"><span data-stu-id="55310-197">Once the request stream is read for a parameter, it's generally not possible to read the request stream again for binding other `[FromBody]` parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="55310-198">`JsonInputFormatter` 为默认格式化程序且基于 [Json.NET](https://www.newtonsoft.com/json)。</span><span class="sxs-lookup"><span data-stu-id="55310-198">The `JsonInputFormatter` is the default formatter and is based on [Json.NET](https://www.newtonsoft.com/json).</span></span>

<span data-ttu-id="55310-199">除非有特性应用于 ASP.NET Core，否则它将基于 [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) 标头和参数类型来选择输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="55310-199">ASP.NET Core selects input formatters based on the [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) header and the type of the parameter, unless there's an attribute applied to it specifying otherwise.</span></span> <span data-ttu-id="55310-200">如果想要使用 XML 或其他格式，则必须在 Startup.cs 文件中配置该格式，但可能必须先使用 NuGet 获取对 `Microsoft.AspNetCore.Mvc.Formatters.Xml` 的引用。</span><span class="sxs-lookup"><span data-stu-id="55310-200">If you'd like to use XML or another format you must configure it in the *Startup.cs* file, but you may first have to obtain a reference to `Microsoft.AspNetCore.Mvc.Formatters.Xml` using NuGet.</span></span> <span data-ttu-id="55310-201">启动代码应如下所示：</span><span class="sxs-lookup"><span data-stu-id="55310-201">Your startup code should look something like this:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

<span data-ttu-id="55310-202">Startup.cs 文件中的代码包含具有 `services` 参数的 `ConfigureServices` 方法，此方法可用于为 ASP.NET Core 应用生成服务。</span><span class="sxs-lookup"><span data-stu-id="55310-202">Code in the *Startup.cs* file contains a `ConfigureServices` method with a `services` argument you can use to build up services for your ASP.NET Core app.</span></span> <span data-ttu-id="55310-203">在此示例中，我们将添加 XML 格式化程序作为 MVC 将为此应用提供的服务。</span><span class="sxs-lookup"><span data-stu-id="55310-203">In the sample, we are adding an XML formatter as a service that MVC will provide for this app.</span></span> <span data-ttu-id="55310-204">通过传递给 `AddMvc` 方法的 `options` 参数，可在应用启动后添加和管理筛选器、格式化程序和其他 MVC 系统选项。</span><span class="sxs-lookup"><span data-stu-id="55310-204">The `options` argument passed into the `AddMvc` method allows you to add and manage filters, formatters, and other system options from MVC upon app startup.</span></span> <span data-ttu-id="55310-205">然后，将 `Consumes` 特性应用于控制器类或操作方法，以使用所需格式。</span><span class="sxs-lookup"><span data-stu-id="55310-205">Then apply the `Consumes` attribute to controller classes or action methods to work with the format you want.</span></span>

### <a name="custom-model-binding"></a><span data-ttu-id="55310-206">自定义模型绑定</span><span class="sxs-lookup"><span data-stu-id="55310-206">Custom Model Binding</span></span>

<span data-ttu-id="55310-207">可通过编写自己的自定义模型绑定器来扩展模型绑定。</span><span class="sxs-lookup"><span data-stu-id="55310-207">You can extend model binding by writing your own custom model binders.</span></span> <span data-ttu-id="55310-208">详细了解[自定义模型绑定](../advanced/custom-model-binding.md)。</span><span class="sxs-lookup"><span data-stu-id="55310-208">Learn more about [custom model binding](../advanced/custom-model-binding.md).</span></span>
