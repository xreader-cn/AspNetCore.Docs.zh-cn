---
<span data-ttu-id="d130d-101">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-102">'Blazor'</span></span>
- <span data-ttu-id="d130d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-103">'Identity'</span></span>
- <span data-ttu-id="d130d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-105">'Razor'</span></span>
- <span data-ttu-id="d130d-106">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="d130d-107">设置 ASP.NET Core Web API 中响应数据的格式</span><span class="sxs-lookup"><span data-stu-id="d130d-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="d130d-108">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d130d-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d130d-109">ASP.NET Core MVC 支持设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="d130d-110">可以使用特定格式或响应客户端请求的格式，来设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="d130d-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d130d-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="d130d-112">特定于格式的操作结果</span><span class="sxs-lookup"><span data-stu-id="d130d-112">Format-specific Action Results</span></span>

<span data-ttu-id="d130d-113">一些操作结果类型特定于特殊格式，例如 <xref:Microsoft.AspNetCore.Mvc.JsonResult> 和 <xref:Microsoft.AspNetCore.Mvc.ContentResult>。</span><span class="sxs-lookup"><span data-stu-id="d130d-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="d130d-114">操作可以返回使用特定格式设置格式的结果，而不考虑客户端首选项。</span><span class="sxs-lookup"><span data-stu-id="d130d-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="d130d-115">例如，返回 `JsonResult`，将返回 JSON 格式的数据。</span><span class="sxs-lookup"><span data-stu-id="d130d-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="d130d-116">返回 `ContentResult` 或字符串，将返回纯文本格式的字符串数据。</span><span class="sxs-lookup"><span data-stu-id="d130d-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="d130d-117">无需操作返回任意特定类型。</span><span class="sxs-lookup"><span data-stu-id="d130d-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="d130d-118">ASP.NET Core 支持任何对象返回值。</span><span class="sxs-lookup"><span data-stu-id="d130d-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="d130d-119">返回非 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 类型对象的操作结果将使用相应的 <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 实现来进行序列化。</span><span class="sxs-lookup"><span data-stu-id="d130d-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="d130d-120">有关详细信息，请参阅 <xref:web-api/action-return-types>。</span><span class="sxs-lookup"><span data-stu-id="d130d-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="d130d-121">内置的帮助程序方法 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> 返回 JSON 格式的数据：[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="d130d-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="d130d-122">示例下载返回作者列表。</span><span class="sxs-lookup"><span data-stu-id="d130d-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="d130d-123">在 F12 浏览器开发人员工具或 [Postman](https://www.getpostman.com/tools) 中使用上述代码：</span><span class="sxs-lookup"><span data-stu-id="d130d-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="d130d-124">将显示包含内容类型的响应标头。\*\*\*\* `application/json; charset=utf-8`</span><span class="sxs-lookup"><span data-stu-id="d130d-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="d130d-125">将显示请求标头。</span><span class="sxs-lookup"><span data-stu-id="d130d-125">The request headers are displayed.</span></span> <span data-ttu-id="d130d-126">例如 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="d130d-126">For example, the `Accept` header.</span></span> <span data-ttu-id="d130d-127">上述代码将忽略 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="d130d-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="d130d-128">若要返回纯文本格式数据，请使用 <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> 和 <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> 帮助程序：</span><span class="sxs-lookup"><span data-stu-id="d130d-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="d130d-129">在上述代码中，返回的 `text/plain` 为 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="d130d-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="d130d-130">返回字符串，将提供 `text/plain` 类型的 `Content-Type`：</span><span class="sxs-lookup"><span data-stu-id="d130d-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="d130d-131">对于包含多个返回类型的操作，将返回 `IActionResult`。</span><span class="sxs-lookup"><span data-stu-id="d130d-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="d130d-132">例如，基于执行的操作的结果返回不同的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="d130d-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="d130d-133">内容协商</span><span class="sxs-lookup"><span data-stu-id="d130d-133">Content negotiation</span></span>

<span data-ttu-id="d130d-134">当客户端指定 [Accept 标头](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)时，会发生内容协商。</span><span class="sxs-lookup"><span data-stu-id="d130d-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="d130d-135">ASP.NET Core 使用的默认格式是 [JSON](https://json.org/)。</span><span class="sxs-lookup"><span data-stu-id="d130d-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="d130d-136">内容协商有以下特点：</span><span class="sxs-lookup"><span data-stu-id="d130d-136">Content negotiation is:</span></span>

* <span data-ttu-id="d130d-137">由 <xref:Microsoft.AspNetCore.Mvc.ObjectResult> 实现。</span><span class="sxs-lookup"><span data-stu-id="d130d-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="d130d-138">内置于从帮助程序方法返回的特定于状态代码的操作结果中。</span><span class="sxs-lookup"><span data-stu-id="d130d-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="d130d-139">操作结果帮助程序方法基于 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="d130d-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="d130d-140">返回模型类型后，返回类型为 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="d130d-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="d130d-141">以下操作方法使用 `Ok` 和 `NotFound` 帮助程序方法：</span><span class="sxs-lookup"><span data-stu-id="d130d-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="d130d-142">默认情况下，ASP.NET Core 支持 `application/json`、`text/json` 和 `text/plain` 媒体类型。</span><span class="sxs-lookup"><span data-stu-id="d130d-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="d130d-143">[Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/tools) 等工具可以设置 `Accept` 请求标头，来指定返回格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="d130d-144">`Accept` 标头包含服务器支持的类型时，将返回该类型。</span><span class="sxs-lookup"><span data-stu-id="d130d-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="d130d-145">下一节将介绍如何添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="d130d-146">控制器操作可以返回 POCO（普通旧 CLR 对象）。</span><span class="sxs-lookup"><span data-stu-id="d130d-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="d130d-147">返回 POCO 时，运行时自动创建包装该对象的 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="d130d-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="d130d-148">客户端将获得已格式化和序列化的对象。</span><span class="sxs-lookup"><span data-stu-id="d130d-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="d130d-149">若将返回的对象为 `null`，将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="d130d-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="d130d-150">返回对象类型：</span><span class="sxs-lookup"><span data-stu-id="d130d-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="d130d-151">在前面的代码中，请求有效作者别名将返回具有作者数据的 `200 OK` 响应。</span><span class="sxs-lookup"><span data-stu-id="d130d-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="d130d-152">请求无效别名将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="d130d-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="d130d-153">Accept 标头</span><span class="sxs-lookup"><span data-stu-id="d130d-153">The Accept header</span></span>

<span data-ttu-id="d130d-154">内容协商在 `Accept` 标头出现在请求中时发生\*\*。</span><span class="sxs-lookup"><span data-stu-id="d130d-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="d130d-155">请求包含 Accept 标头时，ASP.NET Core 将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d130d-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="d130d-156">按首选顺序枚举 Accept 标头中的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="d130d-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="d130d-157">尝试找到可以生成某种指定格式的响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="d130d-158">若未找到可以满足客户端请求的格式化程序，ASP.NET Core 将指定以下操作：</span><span class="sxs-lookup"><span data-stu-id="d130d-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="d130d-159">已设置 `406 Not Acceptable` 时，将返回 <xref:Microsoft.AspNetCore.Mvc.MvcOptions>，或者</span><span class="sxs-lookup"><span data-stu-id="d130d-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="d130d-160">尝试找到第一个可以生成响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="d130d-161">如果没有配置实现所请求格式的格式化程序，那么使用第一个可以设置对象格式的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="d130d-162">若请求中没有 `Accept` 标头：</span><span class="sxs-lookup"><span data-stu-id="d130d-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="d130d-163">将使用第一个可以处理对象的格式化程序来将响应序列化。</span><span class="sxs-lookup"><span data-stu-id="d130d-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="d130d-164">不执行任何协商。</span><span class="sxs-lookup"><span data-stu-id="d130d-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="d130d-165">服务器将决定要返回的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-165">The server is determining what format to return.</span></span>

<span data-ttu-id="d130d-166">如果 Accept 标头包含 `*/*`，则将忽略该标头，除非 `RespectBrowserAcceptHeader` 在 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 上设置为 true。</span><span class="sxs-lookup"><span data-stu-id="d130d-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="d130d-167">浏览器和内容协商</span><span class="sxs-lookup"><span data-stu-id="d130d-167">Browsers and content negotiation</span></span>

<span data-ttu-id="d130d-168">与典型的 API 客户端不同的是，Web 浏览器提供 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="d130d-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="d130d-169">Web 浏览器指定多种格式，包括通配符。</span><span class="sxs-lookup"><span data-stu-id="d130d-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="d130d-170">默认情况下，当框架检测到请求来自浏览器时，将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d130d-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="d130d-171">忽略 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="d130d-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="d130d-172">若未另行配置，将使用 JSON 返回内容。</span><span class="sxs-lookup"><span data-stu-id="d130d-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="d130d-173">这样，在使用 API 时，浏览器中的体验将更加一致。</span><span class="sxs-lookup"><span data-stu-id="d130d-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="d130d-174">若要将应用配置为采用浏览器 Accept 标头，请将 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="d130d-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="d130d-175">配置格式化程序</span><span class="sxs-lookup"><span data-stu-id="d130d-175">Configure formatters</span></span>

<span data-ttu-id="d130d-176">需要支持其他格式的应用可以添加相应的 NuGet 包，并配置支持。</span><span class="sxs-lookup"><span data-stu-id="d130d-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="d130d-177">输入和输出的格式化程序不同。</span><span class="sxs-lookup"><span data-stu-id="d130d-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="d130d-178">[模型绑定](xref:mvc/models/model-binding)使用输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="d130d-179">格式响应使用输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="d130d-180">有关创建自定义格式化程序的信息，请参阅[自定义格式化程序](xref:web-api/advanced/custom-formatters)。</span><span class="sxs-lookup"><span data-stu-id="d130d-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="d130d-181">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="d130d-181">Add XML format support</span></span>

<span data-ttu-id="d130d-182">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> 来配置使用 <xref:System.Xml.Serialization.XmlSerializer> 实现的 XML 格式化程序：</span><span class="sxs-lookup"><span data-stu-id="d130d-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="d130d-183">前面的代码将使用 `XmlSerializer` 将结果序列化。</span><span class="sxs-lookup"><span data-stu-id="d130d-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="d130d-184">使用前面的代码时，控制器方法会基于请求的 `Accept` 标头返回相应的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="d130d-185">配置基于 System.Text.Json 的格式化程序</span><span class="sxs-lookup"><span data-stu-id="d130d-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="d130d-186">可以使用 `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` 配置基于 `System.Text.Json` 的格式化程序的功能。</span><span class="sxs-lookup"><span data-stu-id="d130d-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="d130d-187">可以使用 `JsonResult` 配置基于每个操作的输出序列化选项。</span><span class="sxs-lookup"><span data-stu-id="d130d-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="d130d-188">例如：</span><span class="sxs-lookup"><span data-stu-id="d130d-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="d130d-189">添加基于 Newtonsoft.Json 的 JSON 格式支持</span><span class="sxs-lookup"><span data-stu-id="d130d-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="d130d-190">ASP.NET Core 3.0 之前的版本中，默认设置使用通过 `Newtonsoft.Json` 包实现的 JSON 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="d130d-191">在 ASP.NET Core 3.0 或更高版本中，默认 JSON 格式化程序基于 `System.Text.Json`。</span><span class="sxs-lookup"><span data-stu-id="d130d-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="d130d-192">`Newtonsoft.Json`通过安装 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 包并在中进行配置，可获得对基于的格式化程序和功能的支持 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="d130d-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="d130d-193">某些功能可能不适用于基于 `System.Text.Json` 的格式化程序，而需要引用基于 `Newtonsoft.Json` 的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="d130d-194">若应用符合以下情况，请继续使用基于 `Newtonsoft.Json` 的格式化程序：</span><span class="sxs-lookup"><span data-stu-id="d130d-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="d130d-195">使用 `Newtonsoft.Json` 属性。</span><span class="sxs-lookup"><span data-stu-id="d130d-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="d130d-196">例如，`[JsonProperty]` 或 `[JsonIgnore]`。</span><span class="sxs-lookup"><span data-stu-id="d130d-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="d130d-197">自定义序列化设置。</span><span class="sxs-lookup"><span data-stu-id="d130d-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="d130d-198">依赖 `Newtonsoft.Json` 提供的功能。</span><span class="sxs-lookup"><span data-stu-id="d130d-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="d130d-199">配置 `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`。</span><span class="sxs-lookup"><span data-stu-id="d130d-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="d130d-200">ASP.NET Core 3.0 之前的版本中，`JsonResult.SerializerSettings` 接受特定于 `Newtonsoft.Json` 的 `JsonSerializerSettings` 的实例。</span><span class="sxs-lookup"><span data-stu-id="d130d-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="d130d-201">生成 [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 文档。</span><span class="sxs-lookup"><span data-stu-id="d130d-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="d130d-202">可以使用 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` 配置基于 `Newtonsoft.Json` 的格式化程序的功能：</span><span class="sxs-lookup"><span data-stu-id="d130d-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="d130d-203">可以使用 `JsonResult` 配置基于每个操作的输出序列化选项。</span><span class="sxs-lookup"><span data-stu-id="d130d-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="d130d-204">例如：</span><span class="sxs-lookup"><span data-stu-id="d130d-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="d130d-205">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="d130d-205">Add XML format support</span></span>

<span data-ttu-id="d130d-206">XML 格式需要 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d130d-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="d130d-207">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> 来配置使用 <xref:System.Xml.Serialization.XmlSerializer> 实现的 XML 格式化程序：</span><span class="sxs-lookup"><span data-stu-id="d130d-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="d130d-208">前面的代码将使用 `XmlSerializer` 将结果序列化。</span><span class="sxs-lookup"><span data-stu-id="d130d-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="d130d-209">使用前面的代码时，控制器方法应基于请求的 `Accept` 标头返回相应的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="d130d-210">指定格式</span><span class="sxs-lookup"><span data-stu-id="d130d-210">Specify a format</span></span>

<span data-ttu-id="d130d-211">若要限制响应格式，请应用 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 筛选器。</span><span class="sxs-lookup"><span data-stu-id="d130d-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="d130d-212">与大多数[筛选器](xref:mvc/controllers/filters)一样， `[Produces]` 可以在操作、控制器或全局范围内应用：</span><span class="sxs-lookup"><span data-stu-id="d130d-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="d130d-213">上述 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 筛选器：</span><span class="sxs-lookup"><span data-stu-id="d130d-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="d130d-214">强制控制器内的所有操作返回 JSON 格式的响应。</span><span class="sxs-lookup"><span data-stu-id="d130d-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="d130d-215">若已配置其他格式化程序，并且客户端指定了其他格式，将返回 JSON。</span><span class="sxs-lookup"><span data-stu-id="d130d-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="d130d-216">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)。</span><span class="sxs-lookup"><span data-stu-id="d130d-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="d130d-217">特例格式化程序</span><span class="sxs-lookup"><span data-stu-id="d130d-217">Special case formatters</span></span>

<span data-ttu-id="d130d-218">一些特例是使用内置格式化程序实现的。</span><span class="sxs-lookup"><span data-stu-id="d130d-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="d130d-219">默认情况下，`string` 返回类型的格式将设为 text/plain（如果通过 `Accept` 标头请求则为 text/html）\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="d130d-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="d130d-220">可以通过删除 <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="d130d-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="d130d-221">在 `ConfigureServices` 方法中删除格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="d130d-222">有模型对象返回类型的操作将在返回 `null` 时返回 `204 No Content`。</span><span class="sxs-lookup"><span data-stu-id="d130d-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="d130d-223">可以通过删除 <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="d130d-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="d130d-224">以下代码删除 `StringOutputFormatter` 和 `HttpNoContentOutputFormatter`。</span><span class="sxs-lookup"><span data-stu-id="d130d-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="d130d-225">如果没有 `StringOutputFormatter`，内置 JSON 格式化程序将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="d130d-226">如果删除了内置 JSON 格式化程序并提供了 XML 格式化程序，则 XML 格式化程序将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="d130d-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="d130d-227">否则，`string` 返回类型返回 `406 Not Acceptable`。</span><span class="sxs-lookup"><span data-stu-id="d130d-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="d130d-228">没有 `HttpNoContentOutputFormatter`，null 对象将使用配置的格式化程序来进行格式设置。</span><span class="sxs-lookup"><span data-stu-id="d130d-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="d130d-229">例如：</span><span class="sxs-lookup"><span data-stu-id="d130d-229">For example:</span></span>

* <span data-ttu-id="d130d-230">JSON 格式化程序返回正文为 `null` 的响应。</span><span class="sxs-lookup"><span data-stu-id="d130d-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="d130d-231">设置属性 `xsi:nil="true"` 时，XML 格式化程序返回空 XML 元素。</span><span class="sxs-lookup"><span data-stu-id="d130d-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="d130d-232">响应格式 URL 映射</span><span class="sxs-lookup"><span data-stu-id="d130d-232">Response format URL mappings</span></span>

<span data-ttu-id="d130d-233">客户端可以在 URL 中请求特定格式，例如：</span><span class="sxs-lookup"><span data-stu-id="d130d-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="d130d-234">在查询字符串中，或在路径中。</span><span class="sxs-lookup"><span data-stu-id="d130d-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="d130d-235">使用格式特定的文件扩展名，如 .xml 或 .json。</span><span class="sxs-lookup"><span data-stu-id="d130d-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="d130d-236">请求路径的映射必须在 API 使用的路由中指定。</span><span class="sxs-lookup"><span data-stu-id="d130d-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="d130d-237">例如：</span><span class="sxs-lookup"><span data-stu-id="d130d-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="d130d-238">上述路由将允许指定所请求格式为可选文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="d130d-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="d130d-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)特性检查中的格式值是否存在 `RouteData` ，并在创建响应时将响应格式映射到适当的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="d130d-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="d130d-240">路由</span><span class="sxs-lookup"><span data-stu-id="d130d-240">Route</span></span>        |             <span data-ttu-id="d130d-241">格式化程序</span><span class="sxs-lookup"><span data-stu-id="d130d-241">Formatter</span></span>              |
|---
<span data-ttu-id="d130d-242">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-243">'Blazor'</span></span>
- <span data-ttu-id="d130d-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-244">'Identity'</span></span>
- <span data-ttu-id="d130d-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-246">'Razor'</span></span>
- <span data-ttu-id="d130d-247">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-248">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-249">'Blazor'</span></span>
- <span data-ttu-id="d130d-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-250">'Identity'</span></span>
- <span data-ttu-id="d130d-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-252">'Razor'</span></span>
- <span data-ttu-id="d130d-253">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-254">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-255">'Blazor'</span></span>
- <span data-ttu-id="d130d-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-256">'Identity'</span></span>
- <span data-ttu-id="d130d-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-258">'Razor'</span></span>
- <span data-ttu-id="d130d-259">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-260">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-261">'Blazor'</span></span>
- <span data-ttu-id="d130d-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-262">'Identity'</span></span>
- <span data-ttu-id="d130d-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-264">'Razor'</span></span>
- <span data-ttu-id="d130d-265">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-266">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-267">'Blazor'</span></span>
- <span data-ttu-id="d130d-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-268">'Identity'</span></span>
- <span data-ttu-id="d130d-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-270">'Razor'</span></span>
- <span data-ttu-id="d130d-271">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-272">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-273">'Blazor'</span></span>
- <span data-ttu-id="d130d-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-274">'Identity'</span></span>
- <span data-ttu-id="d130d-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-276">'Razor'</span></span>
- <span data-ttu-id="d130d-277">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-278">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-279">'Blazor'</span></span>
- <span data-ttu-id="d130d-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-280">'Identity'</span></span>
- <span data-ttu-id="d130d-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-282">'Razor'</span></span>
- <span data-ttu-id="d130d-283">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-284">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-285">'Blazor'</span></span>
- <span data-ttu-id="d130d-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-286">'Identity'</span></span>
- <span data-ttu-id="d130d-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-288">'Razor'</span></span>
- <span data-ttu-id="d130d-289">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-290">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-291">'Blazor'</span></span>
- <span data-ttu-id="d130d-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-292">'Identity'</span></span>
- <span data-ttu-id="d130d-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-294">'Razor'</span></span>
- <span data-ttu-id="d130d-295">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-296">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-297">'Blazor'</span></span>
- <span data-ttu-id="d130d-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-298">'Identity'</span></span>
- <span data-ttu-id="d130d-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-300">'Razor'</span></span>
- <span data-ttu-id="d130d-301">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-301">'SignalR' uid:</span></span> 

<span data-ttu-id="d130d-302">------------|---标题：作者：说明： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-303">'Blazor'</span></span>
- <span data-ttu-id="d130d-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-304">'Identity'</span></span>
- <span data-ttu-id="d130d-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-306">'Razor'</span></span>
- <span data-ttu-id="d130d-307">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-308">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-309">'Blazor'</span></span>
- <span data-ttu-id="d130d-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-310">'Identity'</span></span>
- <span data-ttu-id="d130d-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-312">'Razor'</span></span>
- <span data-ttu-id="d130d-313">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-314">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-315">'Blazor'</span></span>
- <span data-ttu-id="d130d-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-316">'Identity'</span></span>
- <span data-ttu-id="d130d-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-318">'Razor'</span></span>
- <span data-ttu-id="d130d-319">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-320">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-321">'Blazor'</span></span>
- <span data-ttu-id="d130d-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-322">'Identity'</span></span>
- <span data-ttu-id="d130d-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-324">'Razor'</span></span>
- <span data-ttu-id="d130d-325">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-326">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-327">'Blazor'</span></span>
- <span data-ttu-id="d130d-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-328">'Identity'</span></span>
- <span data-ttu-id="d130d-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-330">'Razor'</span></span>
- <span data-ttu-id="d130d-331">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-332">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-333">'Blazor'</span></span>
- <span data-ttu-id="d130d-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-334">'Identity'</span></span>
- <span data-ttu-id="d130d-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-336">'Razor'</span></span>
- <span data-ttu-id="d130d-337">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-338">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-339">'Blazor'</span></span>
- <span data-ttu-id="d130d-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-340">'Identity'</span></span>
- <span data-ttu-id="d130d-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-342">'Razor'</span></span>
- <span data-ttu-id="d130d-343">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-344">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-345">'Blazor'</span></span>
- <span data-ttu-id="d130d-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-346">'Identity'</span></span>
- <span data-ttu-id="d130d-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-348">'Razor'</span></span>
- <span data-ttu-id="d130d-349">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-350">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-351">'Blazor'</span></span>
- <span data-ttu-id="d130d-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-352">'Identity'</span></span>
- <span data-ttu-id="d130d-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-354">'Razor'</span></span>
- <span data-ttu-id="d130d-355">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-356">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-357">'Blazor'</span></span>
- <span data-ttu-id="d130d-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-358">'Identity'</span></span>
- <span data-ttu-id="d130d-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-360">'Razor'</span></span>
- <span data-ttu-id="d130d-361">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-362">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-363">'Blazor'</span></span>
- <span data-ttu-id="d130d-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-364">'Identity'</span></span>
- <span data-ttu-id="d130d-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-366">'Razor'</span></span>
- <span data-ttu-id="d130d-367">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-368">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-369">'Blazor'</span></span>
- <span data-ttu-id="d130d-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-370">'Identity'</span></span>
- <span data-ttu-id="d130d-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-372">'Razor'</span></span>
- <span data-ttu-id="d130d-373">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-374">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-375">'Blazor'</span></span>
- <span data-ttu-id="d130d-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-376">'Identity'</span></span>
- <span data-ttu-id="d130d-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-378">'Razor'</span></span>
- <span data-ttu-id="d130d-379">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-380">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-381">'Blazor'</span></span>
- <span data-ttu-id="d130d-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-382">'Identity'</span></span>
- <span data-ttu-id="d130d-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-384">'Razor'</span></span>
- <span data-ttu-id="d130d-385">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-386">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-387">'Blazor'</span></span>
- <span data-ttu-id="d130d-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-388">'Identity'</span></span>
- <span data-ttu-id="d130d-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-390">'Razor'</span></span>
- <span data-ttu-id="d130d-391">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="d130d-392">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="d130d-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="d130d-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="d130d-393">'Blazor'</span></span>
- <span data-ttu-id="d130d-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="d130d-394">'Identity'</span></span>
- <span data-ttu-id="d130d-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="d130d-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="d130d-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="d130d-396">'Razor'</span></span>
- <span data-ttu-id="d130d-397">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="d130d-397">'SignalR' uid:</span></span> 

<span data-ttu-id="d130d-398">------------------| |  `/api/products/5`    |   默认输出格式化程序 | |`/api/products/5.json` |JSON 格式化程序（如果已配置） | |`/api/products/5.xml`  |XML 格式化程序（如果已配置） |</span><span class="sxs-lookup"><span data-stu-id="d130d-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
