---
title: 设置 ASP.NET Core Web API 中响应数据的格式
author: ardalis
description: 了解如何设置 ASP.NET Core Web API 中响应数据的格式。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 10/14/2016
uid: web-api/advanced/formatting
ms.openlocfilehash: 04f5b3c544cf3fc47c8321c8233535400fcf55f4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890762"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="b8087-103">设置 ASP.NET Core Web API 中响应数据的格式</span><span class="sxs-lookup"><span data-stu-id="b8087-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="b8087-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b8087-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b8087-105">ASP.NET Core MVC 包含对通过固定格式或根据客户端规范来设置响应数据格式的内置支持。</span><span class="sxs-lookup"><span data-stu-id="b8087-105">ASP.NET Core MVC has built-in support for formatting response data, using fixed formats or in response to client specifications.</span></span>

<span data-ttu-id="b8087-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b8087-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="b8087-107">特定于格式的操作结果</span><span class="sxs-lookup"><span data-stu-id="b8087-107">Format-Specific Action Results</span></span>

<span data-ttu-id="b8087-108">一些操作结果类型特定于特殊格式，例如 `JsonResult` 和 `ContentResult`。</span><span class="sxs-lookup"><span data-stu-id="b8087-108">Some action result types are specific to a particular format, such as `JsonResult` and `ContentResult`.</span></span> <span data-ttu-id="b8087-109">操作可以返回始终以特定方式进行格式设置的特定结果。</span><span class="sxs-lookup"><span data-stu-id="b8087-109">Actions can return specific results that are always formatted in a particular manner.</span></span> <span data-ttu-id="b8087-110">例如，返回 `JsonResult` 将返回 JSON 格式的数据，而不考虑客户端首选项。</span><span class="sxs-lookup"><span data-stu-id="b8087-110">For example, returning a `JsonResult` will return JSON-formatted data, regardless of client preferences.</span></span> <span data-ttu-id="b8087-111">同样，返回 `ContentResult` 将返回纯文本格式的字符串数据（仅返回字符串也是如此）。</span><span class="sxs-lookup"><span data-stu-id="b8087-111">Likewise, returning a `ContentResult` will return plain-text-formatted string data (as will simply returning a string).</span></span>

> [!NOTE]
> <span data-ttu-id="b8087-112">不需要操作返回任何特定类型；MVC 支持任意对象返回值。</span><span class="sxs-lookup"><span data-stu-id="b8087-112">An action isn't required to return any particular type; MVC supports any object return value.</span></span> <span data-ttu-id="b8087-113">如果操作返回 `IActionResult` 实现且控制器继承自 `Controller` ，则开发人员具有对应于多种选择的帮助程序方法。</span><span class="sxs-lookup"><span data-stu-id="b8087-113">If an action returns an `IActionResult` implementation and the controller inherits from `Controller`, developers have many helper methods corresponding to many of the choices.</span></span> <span data-ttu-id="b8087-114">返回非 `IActionResult` 类型对象的操作结果将使用相应的 `IOutputFormatter` 实现来进行序列化。</span><span class="sxs-lookup"><span data-stu-id="b8087-114">Results from actions that return objects that are not `IActionResult` types will be serialized using the appropriate `IOutputFormatter` implementation.</span></span>

<span data-ttu-id="b8087-115">若要从继承自 `Controller` 基类的控制器返回特定格式的数据，请使用内置的帮助程序方法 `Json` 来返回 JSON，并使用 `Content` 来返回纯文本。</span><span class="sxs-lookup"><span data-stu-id="b8087-115">To return data in a specific format from a controller that inherits from the `Controller` base class, use the built-in helper method `Json` to return JSON and `Content` for plain text.</span></span> <span data-ttu-id="b8087-116">操作方法应返回特定的结果类型（例如 `JsonResult`）或 `IActionResult`）。</span><span class="sxs-lookup"><span data-stu-id="b8087-116">Your action method should return either the specific result type (for instance, `JsonResult`) or `IActionResult`.</span></span>

<span data-ttu-id="b8087-117">返回 JSON 格式的数据：</span><span class="sxs-lookup"><span data-stu-id="b8087-117">Returning JSON-formatted data:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=21-26)]

<span data-ttu-id="b8087-118">此操作的示例响应：</span><span class="sxs-lookup"><span data-stu-id="b8087-118">Sample response from this action:</span></span>

![Microsoft Edge 中开发人员工具的“网络”选项卡显示响应的内容类型为 application/json](formatting/_static/json-response.png)

<span data-ttu-id="b8087-120">请注意，响应的内容类型为 `application/json`，网络请求的列表和“响应标头”部分均有显示。</span><span class="sxs-lookup"><span data-stu-id="b8087-120">Note that the content type of the response is `application/json`, shown both in the list of network requests and in the Response Headers section.</span></span> <span data-ttu-id="b8087-121">另请注意“响应标头”部分 Accept 标头的浏览器（此示例中为 Microsoft Edge）所呈现的选项列表。</span><span class="sxs-lookup"><span data-stu-id="b8087-121">Also note the list of options presented by the browser (in this case, Microsoft Edge) in the Accept header in the Request Headers section.</span></span> <span data-ttu-id="b8087-122">当前技术忽略此标头；下面将讨论遵守它的情况。</span><span class="sxs-lookup"><span data-stu-id="b8087-122">The current technique is ignoring this header; obeying it is discussed below.</span></span>

<span data-ttu-id="b8087-123">若要返回纯文本格式数据，请使用 `ContentResult` 和 `Content` 帮助程序：</span><span class="sxs-lookup"><span data-stu-id="b8087-123">To return plain text formatted data, use `ContentResult` and the `Content` helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=47-52)]

<span data-ttu-id="b8087-124">此操作的响应：</span><span class="sxs-lookup"><span data-stu-id="b8087-124">A response from this action:</span></span>

![Microsoft Edge 中开发人员工具的“网络”选项卡显示响应的内容类型为 text/plain](formatting/_static/text-response.png)

<span data-ttu-id="b8087-126">请注意此示例中返回的 `Content-Type` 为 `text/plain`。</span><span class="sxs-lookup"><span data-stu-id="b8087-126">Note in this case the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="b8087-127">还可以仅使用字符串响应类型来达到此相同行为：</span><span class="sxs-lookup"><span data-stu-id="b8087-127">You can also achieve this same behavior using just a string response type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3,5&range=54-59)]

>[!TIP]
> <span data-ttu-id="b8087-128">对于有多个返回类型或选项的重要操作（例如基于所执行操作的结果的不同 HTTP 状态代码），请首选 `IActionResult` 作为返回类型。</span><span class="sxs-lookup"><span data-stu-id="b8087-128">For non-trivial actions with multiple return types or options (for example, different HTTP status codes based on the result of operations performed), prefer `IActionResult` as the return type.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="b8087-129">内容协商</span><span class="sxs-lookup"><span data-stu-id="b8087-129">Content Negotiation</span></span>

<span data-ttu-id="b8087-130">当客户端指定 [Accept 标头](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)时，会发生内容协商（简称 conneg）。</span><span class="sxs-lookup"><span data-stu-id="b8087-130">Content negotiation (*conneg* for short) occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="b8087-131">ASP.NET Core MVC 使用的默认格式是 JSON。</span><span class="sxs-lookup"><span data-stu-id="b8087-131">The default format used by ASP.NET Core MVC is JSON.</span></span> <span data-ttu-id="b8087-132">内容协商由 `ObjectResult` 实现。</span><span class="sxs-lookup"><span data-stu-id="b8087-132">Content negotiation is implemented by `ObjectResult`.</span></span> <span data-ttu-id="b8087-133">它还内置于从帮助程序方法（全部基于 `ObjectResult`）返回的特定于状态代码的操作结果中。</span><span class="sxs-lookup"><span data-stu-id="b8087-133">It's also built into the status code specific action results returned from the helper methods (which are all based on `ObjectResult`).</span></span> <span data-ttu-id="b8087-134">还可以返回一个模型类型（已定义为数据传输类型的类），框架将自动将其打包在 `ObjectResult` 中。</span><span class="sxs-lookup"><span data-stu-id="b8087-134">You can also return a model type (a class you've defined as your data transfer type) and the framework will automatically wrap it in an `ObjectResult` for you.</span></span>

<span data-ttu-id="b8087-135">以下操作方法使用 `Ok` 和 `NotFound` 帮助程序方法：</span><span class="sxs-lookup"><span data-stu-id="b8087-135">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=8,10&range=28-38)]

<span data-ttu-id="b8087-136">将返回 JSON 格式的响应，除非请求了另一个格式且服务器可以返回所请求格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-136">A JSON-formatted response will be returned unless another format was requested and the server can return the requested format.</span></span> <span data-ttu-id="b8087-137">可以使用 [Fiddler](http://www.telerik.com/fiddler) 等工具创建包括 Accept 标头的请求并指定另一种格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-137">You can use a tool like [Fiddler](http://www.telerik.com/fiddler) to create a request that includes an Accept header and specify another format.</span></span> <span data-ttu-id="b8087-138">在此情况下，如果服务器有可以生成所请求格式的响应的格式化程序，则结果会以服务器首选的格式返回。</span><span class="sxs-lookup"><span data-stu-id="b8087-138">In that case, if the server has a *formatter* that can produce a response in the requested format, the result will be returned in the client-preferred format.</span></span>

![Fiddler 控制台显示 Accept 标头为 application/xml 的手动创建的 GET 请求](formatting/_static/fiddler-composer.png)

<span data-ttu-id="b8087-140">在上面的屏幕快照中，使用 Fiddler Composer 生成请求，指定 `Accept: application/xml`。</span><span class="sxs-lookup"><span data-stu-id="b8087-140">In the above screenshot, the Fiddler Composer has been used to generate a request, specifying `Accept: application/xml`.</span></span> <span data-ttu-id="b8087-141">默认情况下，ASP.NET Core MVC 仅支持 JSON。所以，即使指定另一种格式，返回的结果仍然是 JSON 格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-141">By default, ASP.NET Core MVC only supports JSON, so even when another format is specified, the result returned is still JSON-formatted.</span></span> <span data-ttu-id="b8087-142">将在下一节中看到如何添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="b8087-142">You'll see how to add additional formatters in the next section.</span></span>

<span data-ttu-id="b8087-143">控制器操作可以返回 POCO（普通旧 CLR 对象），在这种情况下，ASP.NET Core MVC 将自动创建打包对象的 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="b8087-143">Controller actions can return POCOs (Plain Old CLR Objects), in which case ASP.NET Core MVC automatically creates an `ObjectResult` for you that wraps the object.</span></span> <span data-ttu-id="b8087-144">客户端将获取设有格式的序列化对象（默认为 JSON 格式，可以配置 XML 或其他格式）。</span><span class="sxs-lookup"><span data-stu-id="b8087-144">The client will get the formatted serialized object (JSON format is the default; you can configure XML or other formats).</span></span> <span data-ttu-id="b8087-145">如果返回的对象为 `null`，那么框架将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="b8087-145">If the object being returned is `null`, then the framework will return a `204 No Content` response.</span></span>

<span data-ttu-id="b8087-146">返回对象类型：</span><span class="sxs-lookup"><span data-stu-id="b8087-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/Api/AuthorsController.cs?highlight=3&range=40-45)]

<span data-ttu-id="b8087-147">在示例中，请求有效作者别名将收到具有作者数据的“200 正常”响应。</span><span class="sxs-lookup"><span data-stu-id="b8087-147">In the sample, a request for a valid author alias will receive a 200 OK response with the author's data.</span></span> <span data-ttu-id="b8087-148">请求无效别名将收到“204 无内容”响应。</span><span class="sxs-lookup"><span data-stu-id="b8087-148">A request for an invalid alias will receive a 204 No Content response.</span></span> <span data-ttu-id="b8087-149">下面列出了以 XML 和 JSON 格式显示响应的屏幕快照。</span><span class="sxs-lookup"><span data-stu-id="b8087-149">Screenshots showing the response in XML and JSON formats are shown below.</span></span>

### <a name="content-negotiation-process"></a><span data-ttu-id="b8087-150">内容协商过程</span><span class="sxs-lookup"><span data-stu-id="b8087-150">Content Negotiation Process</span></span>

<span data-ttu-id="b8087-151">内容协商仅在 `Accept` 标头出现在请求中时发生。</span><span class="sxs-lookup"><span data-stu-id="b8087-151">Content *negotiation* only takes place if an `Accept` header appears in the request.</span></span> <span data-ttu-id="b8087-152">请求包含 accept 标头时，框架会以最佳顺序枚举 accept 标头中的媒体类型，并且尝试查找可以生成一种由 accept 标头指定格式的响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="b8087-152">When a request contains an accept header, the framework will enumerate the media types in the accept header in preference order and will try to find a formatter that can produce a response in one of the formats specified by the accept header.</span></span> <span data-ttu-id="b8087-153">如果未找到可以满足客户端请求的格式化程序，框架将尝试找到第一个可以生成响应的格式化程序（除非开发人员配置 `MvcOptions` 上的选项以返回“406 不可接受”）。</span><span class="sxs-lookup"><span data-stu-id="b8087-153">In case no formatter is found that can satisfy the client's request, the framework will try to find the first formatter that can produce a response (unless the developer has configured the option on `MvcOptions` to return 406 Not Acceptable instead).</span></span> <span data-ttu-id="b8087-154">如果请求指定 XML，但是未配置 XML 格式化程序，那么将使用 JSON 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="b8087-154">If the request specifies XML, but the XML formatter has not been configured, then the JSON formatter will be used.</span></span> <span data-ttu-id="b8087-155">一般来说，如果没有配置可以提供所请求格式的格式化程序，那么使用第一个可以设置对象格式的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="b8087-155">More generally, if no formatter is configured that can provide the requested format, then the first formatter that can format the object is used.</span></span> <span data-ttu-id="b8087-156">如果不提供任何标头，则将使用第一个可以处理要返回的对象的格式化程序来序列化响应。</span><span class="sxs-lookup"><span data-stu-id="b8087-156">If no header is given, the first formatter that can handle the object to be returned will be used to serialize the response.</span></span> <span data-ttu-id="b8087-157">在此情况下，没有任何协商发生 - 服务器确定将使用的格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-157">In this case, there isn't any negotiation taking place - the server is determining what format it will use.</span></span>

> [!NOTE]
> <span data-ttu-id="b8087-158">如果 Accept 标头包含 `*/*`，则将忽略该标头，除非 `RespectBrowserAcceptHeader` 在 `MvcOptions` 上设置为 true。</span><span class="sxs-lookup"><span data-stu-id="b8087-158">If the Accept header contains `*/*`, the Header will be ignored unless `RespectBrowserAcceptHeader` is set to true on `MvcOptions`.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="b8087-159">浏览器和内容协商</span><span class="sxs-lookup"><span data-stu-id="b8087-159">Browsers and Content Negotiation</span></span>

<span data-ttu-id="b8087-160">不同于传统 API 客户端，Web 浏览器倾向于提供包括各种格式（含通配符）的 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="b8087-160">Unlike typical API clients, web browsers tend to supply `Accept` headers that include a wide array of formats, including wildcards.</span></span> <span data-ttu-id="b8087-161">默认情况下，当框架检测到请求来自浏览器时，它将忽略 `Accept` 标头转而以应用程序的配置默认格式（JSON，除非有其他配置）返回内容。</span><span class="sxs-lookup"><span data-stu-id="b8087-161">By default, when the framework detects that the request is coming from a browser, it will ignore the `Accept` header and instead return the content in the application's configured default format (JSON unless otherwise configured).</span></span> <span data-ttu-id="b8087-162">这在使用不同浏览器使用 API 时提供更一致的体验。</span><span class="sxs-lookup"><span data-stu-id="b8087-162">This provides a more consistent experience when using different browsers to consume APIs.</span></span>

<span data-ttu-id="b8087-163">如果首选应用程序服从浏览器 accept 标头，则可以将此配置为 MVC 配置的一部分，方法是在 Startup.cs 中以 `ConfigureServices` 方法将 `RespectBrowserAcceptHeader` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b8087-163">If you would prefer your application honor browser accept headers, you can configure this as part of MVC's configuration by setting `RespectBrowserAcceptHeader` to `true` in the `ConfigureServices` method in *Startup.cs*.</span></span>

```csharp
services.AddMvc(options =>
{
    options.RespectBrowserAcceptHeader = true; // false by default
});
```

## <a name="configuring-formatters"></a><span data-ttu-id="b8087-164">配置格式化程序</span><span class="sxs-lookup"><span data-stu-id="b8087-164">Configuring Formatters</span></span>

<span data-ttu-id="b8087-165">如果应用程序需要支持默认 JSON 格式以外的其他格式，那么可以添加 NuGet 包并配置 MVC 来支持它们。</span><span class="sxs-lookup"><span data-stu-id="b8087-165">If your application needs to support additional formats beyond the default of JSON, you can add NuGet packages and configure MVC to support them.</span></span> <span data-ttu-id="b8087-166">输入和输出的格式化程序不同。</span><span class="sxs-lookup"><span data-stu-id="b8087-166">There are separate formatters for input and output.</span></span> <span data-ttu-id="b8087-167">输入格式化程序由[模型绑定](xref:mvc/models/model-binding)使用；输出格式化程序用来设置响应格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-167">Input formatters are used by [Model Binding](xref:mvc/models/model-binding); output formatters are used to format responses.</span></span> <span data-ttu-id="b8087-168">还可以配置[自定义格式化程序](xref:web-api/advanced/custom-formatters)。</span><span class="sxs-lookup"><span data-stu-id="b8087-168">You can also configure [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

### <a name="adding-xml-format-support"></a><span data-ttu-id="b8087-169">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="b8087-169">Adding XML Format Support</span></span>

<span data-ttu-id="b8087-170">若要添加对 XML 格式的支持，请安装 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b8087-170">To add support for XML formatting, install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

<span data-ttu-id="b8087-171">将 XmlSerializerFormatters 添加到 Startup.cs 中 MVC 的配置：</span><span class="sxs-lookup"><span data-stu-id="b8087-171">Add the XmlSerializerFormatters to MVC's configuration in *Startup.cs*:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet1&highlight=2)]

<span data-ttu-id="b8087-172">或者，可以仅添加输出格式化程序：</span><span class="sxs-lookup"><span data-stu-id="b8087-172">Alternately, you can add just the output formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlSerializerOutputFormatter());
});
```

<span data-ttu-id="b8087-173">这两个方法将使用 `System.Xml.Serialization.XmlSerializer` 来序列化结果。</span><span class="sxs-lookup"><span data-stu-id="b8087-173">These two approaches will serialize results using `System.Xml.Serialization.XmlSerializer`.</span></span> <span data-ttu-id="b8087-174">如果愿意，可以通过添加相关联的格式化程序使用 `System.Runtime.Serialization.DataContractSerializer`：</span><span class="sxs-lookup"><span data-stu-id="b8087-174">If you prefer, you can use the `System.Runtime.Serialization.DataContractSerializer` by adding its associated formatter:</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.Add(new XmlDataContractSerializerOutputFormatter());
});
```

<span data-ttu-id="b8087-175">一旦添加了对 XML 格式的支持，控制器方法应基于请求的 `Accept` 标头返回相应的格式，如以下 Fiddler 示例所示：</span><span class="sxs-lookup"><span data-stu-id="b8087-175">Once you've added support for XML formatting, your controller methods should return the appropriate format based on the request's `Accept` header, as this Fiddler example demonstrates:</span></span>

![Fiddler 控制台：请求的 Raw 选项卡显示 Accept 标头值为 application/xml。](formatting/_static/xml-response.png)

<span data-ttu-id="b8087-178">可以在“检查器”选项卡中看到使用 `Accept: application/xml` 标头集做出 Raw GET 请求。</span><span class="sxs-lookup"><span data-stu-id="b8087-178">You can see in the Inspectors tab that the Raw GET request was made with an `Accept: application/xml` header set.</span></span> <span data-ttu-id="b8087-179">响应窗格显示 `Content-Type: application/xml` 标头，且 `Author` 对象已序列化为 XML。</span><span class="sxs-lookup"><span data-stu-id="b8087-179">The response pane shows the `Content-Type: application/xml` header, and the `Author` object has been serialized to XML.</span></span>

<span data-ttu-id="b8087-180">使用“编辑器”选项卡修改请求以指定 `Accept` 标头中的 `application/json`。</span><span class="sxs-lookup"><span data-stu-id="b8087-180">Use the Composer tab to modify the request to specify `application/json` in the `Accept` header.</span></span> <span data-ttu-id="b8087-181">执行请求，响应格式将设为 JSON：</span><span class="sxs-lookup"><span data-stu-id="b8087-181">Execute the request, and the response will be formatted as JSON:</span></span>

![Fiddler 控制台：请求的 Raw 选项卡显示 Accept 标头值为 application/json。](formatting/_static/json-response-fiddler.png)

<span data-ttu-id="b8087-184">在此屏幕快照中，可以看到请求了设置 `Accept: application/json` 的标头，且响应也将它指定为其 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="b8087-184">In this screenshot, you can see the request sets a header of `Accept: application/json` and the response specifies the same as its `Content-Type`.</span></span> <span data-ttu-id="b8087-185">`Author` 对象以 JSON 格式显示在响应正文中。</span><span class="sxs-lookup"><span data-stu-id="b8087-185">The `Author` object is shown in the body of the response, in JSON format.</span></span>

### <a name="forcing-a-particular-format"></a><span data-ttu-id="b8087-186">强制执行特定格式</span><span class="sxs-lookup"><span data-stu-id="b8087-186">Forcing a Particular Format</span></span>

<span data-ttu-id="b8087-187">如果需要限制特定操作的响应格式，那么可以应用 `[Produces]` 筛选器。</span><span class="sxs-lookup"><span data-stu-id="b8087-187">If you would like to restrict the response formats for a specific action you can, you can apply the `[Produces]` filter.</span></span> <span data-ttu-id="b8087-188">`[Produces]` 筛选器指定特定操作（或控制器）的响应格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-188">The `[Produces]` filter specifies the response formats for a specific action (or controller).</span></span> <span data-ttu-id="b8087-189">如同大多[筛选器](xref:mvc/controllers/filters)，这可以在操作层面、控制器层面或全局范围内应用。</span><span class="sxs-lookup"><span data-stu-id="b8087-189">Like most [Filters](xref:mvc/controllers/filters), this can be applied at the action, controller, or global scope.</span></span>

```csharp
[Produces("application/json")]
public class AuthorsController
```

<span data-ttu-id="b8087-190">`[Produces]` 筛选器将强制 `AuthorsController` 中的所有操作返回 JSON 格式的响应，即使已经为应用程序配置其他格式化程序且客户端提供了请求其他可用格式的 `Accept` 标头也是如此。</span><span class="sxs-lookup"><span data-stu-id="b8087-190">The `[Produces]` filter will force all actions within the `AuthorsController` to return JSON-formatted responses, even if other formatters were configured for the application and the client provided an `Accept` header requesting a different, available format.</span></span> <span data-ttu-id="b8087-191">请参阅[筛选器](xref:mvc/controllers/filters)了解详细信息，包括如何全局应用筛选器。</span><span class="sxs-lookup"><span data-stu-id="b8087-191">See [Filters](xref:mvc/controllers/filters) to learn more, including how to apply filters globally.</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="b8087-192">特例格式化程序</span><span class="sxs-lookup"><span data-stu-id="b8087-192">Special Case Formatters</span></span>

<span data-ttu-id="b8087-193">一些特例是使用内置格式化程序实现的。</span><span class="sxs-lookup"><span data-stu-id="b8087-193">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="b8087-194">默认情况下，`string` 返回类型的格式将设为 text/plain（如果通过 `Accept` 标头请求则为 text/html）。</span><span class="sxs-lookup"><span data-stu-id="b8087-194">By default, `string` return types will be formatted as *text/plain* (*text/html* if requested via `Accept` header).</span></span> <span data-ttu-id="b8087-195">可以通过删除 `TextOutputFormatter` 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="b8087-195">This behavior can be removed by removing the `TextOutputFormatter`.</span></span> <span data-ttu-id="b8087-196">在 Startup.cs 中通过 `Configure` 方法删除格式化程序（如下所示）。</span><span class="sxs-lookup"><span data-stu-id="b8087-196">You remove formatters in the `Configure` method in *Startup.cs* (shown below).</span></span> <span data-ttu-id="b8087-197">有模型对象返回类型的操作将在返回 `null` 时返回“204 无内容”响应。</span><span class="sxs-lookup"><span data-stu-id="b8087-197">Actions that have a model object return type will return a 204 No Content response when returning `null`.</span></span> <span data-ttu-id="b8087-198">可以通过删除 `HttpNoContentOutputFormatter` 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="b8087-198">This behavior can be removed by removing the `HttpNoContentOutputFormatter`.</span></span> <span data-ttu-id="b8087-199">以下代码删除 `TextOutputFormatter` 和 `HttpNoContentOutputFormatter`。</span><span class="sxs-lookup"><span data-stu-id="b8087-199">The following code removes the `TextOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

```csharp
services.AddMvc(options =>
{
    options.OutputFormatters.RemoveType<TextOutputFormatter>();
    options.OutputFormatters.RemoveType<HttpNoContentOutputFormatter>();
});
```

<span data-ttu-id="b8087-200">没有 `TextOutputFormatter` 时，`string` 返回类型将返回“406 不可接受”等内容。</span><span class="sxs-lookup"><span data-stu-id="b8087-200">Without the `TextOutputFormatter`, `string` return types return 406 Not Acceptable, for example.</span></span> <span data-ttu-id="b8087-201">请注意，如果 XML 格式化程序存在，当删除 `TextOutputFormatter` 时，它将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="b8087-201">Note that if an XML formatter exists, it will format `string` return types if the `TextOutputFormatter` is removed.</span></span>

<span data-ttu-id="b8087-202">没有 `HttpNoContentOutputFormatter`，null 对象将使用配置的格式化程序来进行格式设置。</span><span class="sxs-lookup"><span data-stu-id="b8087-202">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="b8087-203">例如，JSON 格式化程序仅返回正文为 `null` 的响应，而 XML 格式化程序将返回属性设为 `xsi:nil="true"` 的空 XML 元素。</span><span class="sxs-lookup"><span data-stu-id="b8087-203">For example, the JSON formatter will simply return a response with a body of `null`, while the XML formatter will return an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="b8087-204">响应格式 URL 映射</span><span class="sxs-lookup"><span data-stu-id="b8087-204">Response Format URL Mappings</span></span>

<span data-ttu-id="b8087-205">客户端可要求特定格式作为 URL 一部分，例如在查询字符串中或在路径的一部分中，或者通过使用特定格式的文件扩展名（例如 .xml 或 .json）。</span><span class="sxs-lookup"><span data-stu-id="b8087-205">Clients can request a particular format as part of the URL, such as in the query string or part of the path, or by using a format-specific file extension such as .xml or .json.</span></span> <span data-ttu-id="b8087-206">请求路径的映射必须在 API 使用的路由中指定。</span><span class="sxs-lookup"><span data-stu-id="b8087-206">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="b8087-207">例如:</span><span class="sxs-lookup"><span data-stu-id="b8087-207">For example:</span></span>

```csharp
[FormatFilter]
public class ProductsController
{
    [Route("[controller]/[action]/{id}.{format?}")]
    public Product GetById(int id)
```

<span data-ttu-id="b8087-208">此路由将允许所所请求格式指定为可选文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="b8087-208">This route would allow the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="b8087-209">`[FormatFilter]` 属性检查 `RouteData` 中格式值是否存在，并在响应创建时将响应格式映射到相应格式化程序。</span><span class="sxs-lookup"><span data-stu-id="b8087-209">The `[FormatFilter]` attribute checks for the existence of the format value in the `RouteData` and will map the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="b8087-210">路由</span><span class="sxs-lookup"><span data-stu-id="b8087-210">Route</span></span>            |             <span data-ttu-id="b8087-211">格式化程序</span><span class="sxs-lookup"><span data-stu-id="b8087-211">Formatter</span></span>              |
|----------------------------|------------------------------------|
|   `/products/GetById/5`    |    <span data-ttu-id="b8087-212">默认输出格式化程序</span><span class="sxs-lookup"><span data-stu-id="b8087-212">The default output formatter</span></span>    |
| `/products/GetById/5.json` | <span data-ttu-id="b8087-213">JSON 格式化程序（如配置）</span><span class="sxs-lookup"><span data-stu-id="b8087-213">The JSON formatter (if configured)</span></span> |
| `/products/GetById/5.xml`  | <span data-ttu-id="b8087-214">XML 格式化程序（如配置）</span><span class="sxs-lookup"><span data-stu-id="b8087-214">The XML formatter (if configured)</span></span>  |
