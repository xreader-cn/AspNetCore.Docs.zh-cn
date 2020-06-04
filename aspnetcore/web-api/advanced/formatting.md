---
<span data-ttu-id="39030-101">标题： ASP.NET Core Web API 作者： ardalis 说明中的格式响应数据：了解如何在 ASP.NET Core Web API 中设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-101">title: Format response data in ASP.NET Core Web API author: ardalis description: Learn how to format response data in ASP.NET Core Web API.</span></span>
<span data-ttu-id="39030-102">ms-chap： riande： H1Hack27Feb2017 毫秒。日期：04/17/2020 非 loc：</span><span class="sxs-lookup"><span data-stu-id="39030-102">ms.author: riande ms.custom: H1Hack27Feb2017 ms.date: 04/17/2020 no-loc:</span></span>
- <span data-ttu-id="39030-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39030-103">'Blazor'</span></span>
- <span data-ttu-id="39030-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39030-104">'Identity'</span></span>
- <span data-ttu-id="39030-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39030-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="39030-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39030-106">'Razor'</span></span>
- <span data-ttu-id="39030-107">" SignalR " uid： web api/高级/格式</span><span class="sxs-lookup"><span data-stu-id="39030-107">'SignalR' uid: web-api/advanced/formatting</span></span>

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="39030-108">设置 ASP.NET Core Web API 中响应数据的格式</span><span class="sxs-lookup"><span data-stu-id="39030-108">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="39030-109">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="39030-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="39030-110">ASP.NET Core MVC 支持设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-110">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="39030-111">可以使用特定格式或响应客户端请求的格式，来设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-111">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="39030-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="39030-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="39030-113">特定于格式的操作结果</span><span class="sxs-lookup"><span data-stu-id="39030-113">Format-specific Action Results</span></span>

<span data-ttu-id="39030-114">一些操作结果类型特定于特殊格式，例如 <xref:Microsoft.AspNetCore.Mvc.JsonResult> 和 <xref:Microsoft.AspNetCore.Mvc.ContentResult>。</span><span class="sxs-lookup"><span data-stu-id="39030-114">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="39030-115">操作可以返回使用特定格式设置格式的结果，而不考虑客户端首选项。</span><span class="sxs-lookup"><span data-stu-id="39030-115">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="39030-116">例如，返回 `JsonResult`，将返回 JSON 格式的数据。</span><span class="sxs-lookup"><span data-stu-id="39030-116">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="39030-117">返回 `ContentResult` 或字符串，将返回纯文本格式的字符串数据。</span><span class="sxs-lookup"><span data-stu-id="39030-117">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="39030-118">无需操作返回任意特定类型。</span><span class="sxs-lookup"><span data-stu-id="39030-118">An action isn't required to return any specific type.</span></span> <span data-ttu-id="39030-119">ASP.NET Core 支持任何对象返回值。</span><span class="sxs-lookup"><span data-stu-id="39030-119">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="39030-120">返回非 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 类型对象的操作结果将使用相应的 <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 实现来进行序列化。</span><span class="sxs-lookup"><span data-stu-id="39030-120">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="39030-121">有关详细信息，请参阅 <xref:web-api/action-return-types> 。</span><span class="sxs-lookup"><span data-stu-id="39030-121">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="39030-122">内置的帮助程序方法 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> 返回 JSON 格式的数据：[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="39030-122">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="39030-123">示例下载返回作者列表。</span><span class="sxs-lookup"><span data-stu-id="39030-123">The sample download returns the list of authors.</span></span> <span data-ttu-id="39030-124">在 F12 浏览器开发人员工具或 [Postman](https://www.getpostman.com/tools) 中使用上述代码：</span><span class="sxs-lookup"><span data-stu-id="39030-124">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="39030-125">将显示包含内容类型的响应标头。\*\*\*\* `application/json; charset=utf-8`</span><span class="sxs-lookup"><span data-stu-id="39030-125">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="39030-126">将显示请求标头。</span><span class="sxs-lookup"><span data-stu-id="39030-126">The request headers are displayed.</span></span> <span data-ttu-id="39030-127">例如 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="39030-127">For example, the `Accept` header.</span></span> <span data-ttu-id="39030-128">上述代码将忽略 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="39030-128">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="39030-129">若要返回纯文本格式数据，请使用 <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> 和 <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> 帮助程序：</span><span class="sxs-lookup"><span data-stu-id="39030-129">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="39030-130">在上述代码中，返回的 `text/plain` 为 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="39030-130">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="39030-131">返回字符串，将提供 `text/plain` 类型的 `Content-Type`：</span><span class="sxs-lookup"><span data-stu-id="39030-131">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="39030-132">对于包含多个返回类型的操作，将返回 `IActionResult`。</span><span class="sxs-lookup"><span data-stu-id="39030-132">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="39030-133">例如，基于执行的操作的结果返回不同的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="39030-133">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="39030-134">内容协商</span><span class="sxs-lookup"><span data-stu-id="39030-134">Content negotiation</span></span>

<span data-ttu-id="39030-135">当客户端指定 [Accept 标头](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)时，会发生内容协商。</span><span class="sxs-lookup"><span data-stu-id="39030-135">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="39030-136">ASP.NET Core 使用的默认格式是 [JSON](https://json.org/)。</span><span class="sxs-lookup"><span data-stu-id="39030-136">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="39030-137">内容协商有以下特点：</span><span class="sxs-lookup"><span data-stu-id="39030-137">Content negotiation is:</span></span>

* <span data-ttu-id="39030-138">由 <xref:Microsoft.AspNetCore.Mvc.ObjectResult> 实现。</span><span class="sxs-lookup"><span data-stu-id="39030-138">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="39030-139">内置于从帮助程序方法返回的特定于状态代码的操作结果中。</span><span class="sxs-lookup"><span data-stu-id="39030-139">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="39030-140">操作结果帮助程序方法基于 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="39030-140">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="39030-141">返回模型类型后，返回类型为 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="39030-141">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="39030-142">以下操作方法使用 `Ok` 和 `NotFound` 帮助程序方法：</span><span class="sxs-lookup"><span data-stu-id="39030-142">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="39030-143">默认情况下，ASP.NET Core 支持 `application/json`、`text/json` 和 `text/plain` 媒体类型。</span><span class="sxs-lookup"><span data-stu-id="39030-143">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="39030-144">[Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/tools) 等工具可以设置 `Accept` 请求标头，来指定返回格式。</span><span class="sxs-lookup"><span data-stu-id="39030-144">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="39030-145">`Accept` 标头包含服务器支持的类型时，将返回该类型。</span><span class="sxs-lookup"><span data-stu-id="39030-145">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="39030-146">下一节将介绍如何添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-146">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="39030-147">控制器操作可以返回 POCO（普通旧 CLR 对象）。</span><span class="sxs-lookup"><span data-stu-id="39030-147">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="39030-148">返回 POCO 时，运行时自动创建包装该对象的 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="39030-148">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="39030-149">客户端将获得已格式化和序列化的对象。</span><span class="sxs-lookup"><span data-stu-id="39030-149">The client gets the formatted serialized object.</span></span> <span data-ttu-id="39030-150">若将返回的对象为 `null`，将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="39030-150">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="39030-151">返回对象类型：</span><span class="sxs-lookup"><span data-stu-id="39030-151">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="39030-152">在前面的代码中，请求有效作者别名将返回具有作者数据的 `200 OK` 响应。</span><span class="sxs-lookup"><span data-stu-id="39030-152">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="39030-153">请求无效别名将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="39030-153">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="39030-154">Accept 标头</span><span class="sxs-lookup"><span data-stu-id="39030-154">The Accept header</span></span>

<span data-ttu-id="39030-155">内容协商在 `Accept` 标头出现在请求中时发生\*\*。</span><span class="sxs-lookup"><span data-stu-id="39030-155">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="39030-156">请求包含 Accept 标头时，ASP.NET Core 将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="39030-156">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="39030-157">按首选顺序枚举 Accept 标头中的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="39030-157">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="39030-158">尝试找到可以生成某种指定格式的响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-158">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="39030-159">若未找到可以满足客户端请求的格式化程序，ASP.NET Core 将指定以下操作：</span><span class="sxs-lookup"><span data-stu-id="39030-159">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="39030-160">已设置 `406 Not Acceptable` 时，将返回 <xref:Microsoft.AspNetCore.Mvc.MvcOptions>，或者</span><span class="sxs-lookup"><span data-stu-id="39030-160">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="39030-161">尝试找到第一个可以生成响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-161">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="39030-162">如果没有配置实现所请求格式的格式化程序，那么使用第一个可以设置对象格式的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-162">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="39030-163">若请求中没有 `Accept` 标头：</span><span class="sxs-lookup"><span data-stu-id="39030-163">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="39030-164">将使用第一个可以处理对象的格式化程序来将响应序列化。</span><span class="sxs-lookup"><span data-stu-id="39030-164">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="39030-165">不执行任何协商。</span><span class="sxs-lookup"><span data-stu-id="39030-165">There isn't any negotiation taking place.</span></span> <span data-ttu-id="39030-166">服务器将决定要返回的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-166">The server is determining what format to return.</span></span>

<span data-ttu-id="39030-167">如果 Accept 标头包含 `*/*`，则将忽略该标头，除非 `RespectBrowserAcceptHeader` 在 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 上设置为 true。</span><span class="sxs-lookup"><span data-stu-id="39030-167">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="39030-168">浏览器和内容协商</span><span class="sxs-lookup"><span data-stu-id="39030-168">Browsers and content negotiation</span></span>

<span data-ttu-id="39030-169">与典型的 API 客户端不同的是，Web 浏览器提供 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="39030-169">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="39030-170">Web 浏览器指定多种格式，包括通配符。</span><span class="sxs-lookup"><span data-stu-id="39030-170">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="39030-171">默认情况下，当框架检测到请求来自浏览器时，将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="39030-171">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="39030-172">忽略 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="39030-172">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="39030-173">若未另行配置，将使用 JSON 返回内容。</span><span class="sxs-lookup"><span data-stu-id="39030-173">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="39030-174">这样，在使用 API 时，浏览器中的体验将更加一致。</span><span class="sxs-lookup"><span data-stu-id="39030-174">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="39030-175">若要将应用配置为采用浏览器 Accept 标头，请将 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="39030-175">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="39030-176">配置格式化程序</span><span class="sxs-lookup"><span data-stu-id="39030-176">Configure formatters</span></span>

<span data-ttu-id="39030-177">需要支持其他格式的应用可以添加相应的 NuGet 包，并配置支持。</span><span class="sxs-lookup"><span data-stu-id="39030-177">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="39030-178">输入和输出的格式化程序不同。</span><span class="sxs-lookup"><span data-stu-id="39030-178">There are separate formatters for input and output.</span></span> <span data-ttu-id="39030-179">[模型绑定](xref:mvc/models/model-binding)使用输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-179">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="39030-180">格式响应使用输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-180">Output formatters are used to format responses.</span></span> <span data-ttu-id="39030-181">有关创建自定义格式化程序的信息，请参阅[自定义格式化程序](xref:web-api/advanced/custom-formatters)。</span><span class="sxs-lookup"><span data-stu-id="39030-181">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="39030-182">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="39030-182">Add XML format support</span></span>

<span data-ttu-id="39030-183">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> 来配置使用 <xref:System.Xml.Serialization.XmlSerializer> 实现的 XML 格式化程序：</span><span class="sxs-lookup"><span data-stu-id="39030-183">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="39030-184">前面的代码将使用 `XmlSerializer` 将结果序列化。</span><span class="sxs-lookup"><span data-stu-id="39030-184">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="39030-185">使用前面的代码时，控制器方法会基于请求的 `Accept` 标头返回相应的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-185">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="39030-186">配置基于 System.Text.Json 的格式化程序</span><span class="sxs-lookup"><span data-stu-id="39030-186">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="39030-187">可以使用 `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` 配置基于 `System.Text.Json` 的格式化程序的功能。</span><span class="sxs-lookup"><span data-stu-id="39030-187">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="39030-188">可以使用 `JsonResult` 配置基于每个操作的输出序列化选项。</span><span class="sxs-lookup"><span data-stu-id="39030-188">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="39030-189">例如：</span><span class="sxs-lookup"><span data-stu-id="39030-189">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="39030-190">添加基于 Newtonsoft.Json 的 JSON 格式支持</span><span class="sxs-lookup"><span data-stu-id="39030-190">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="39030-191">ASP.NET Core 3.0 之前的版本中，默认设置使用通过 `Newtonsoft.Json` 包实现的 JSON 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-191">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="39030-192">在 ASP.NET Core 3.0 或更高版本中，默认 JSON 格式化程序基于 `System.Text.Json`。</span><span class="sxs-lookup"><span data-stu-id="39030-192">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="39030-193">`Newtonsoft.Json`通过安装 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 包并在中进行配置，可获得对基于的格式化程序和功能的支持 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="39030-193">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="39030-194">某些功能可能不适用于基于 `System.Text.Json` 的格式化程序，而需要引用基于 `Newtonsoft.Json` 的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-194">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="39030-195">若应用符合以下情况，请继续使用基于 `Newtonsoft.Json` 的格式化程序：</span><span class="sxs-lookup"><span data-stu-id="39030-195">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="39030-196">使用 `Newtonsoft.Json` 属性。</span><span class="sxs-lookup"><span data-stu-id="39030-196">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="39030-197">例如，`[JsonProperty]` 或 `[JsonIgnore]`。</span><span class="sxs-lookup"><span data-stu-id="39030-197">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="39030-198">自定义序列化设置。</span><span class="sxs-lookup"><span data-stu-id="39030-198">Customizes the serialization settings.</span></span>
* <span data-ttu-id="39030-199">依赖 `Newtonsoft.Json` 提供的功能。</span><span class="sxs-lookup"><span data-stu-id="39030-199">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="39030-200">配置 `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`。</span><span class="sxs-lookup"><span data-stu-id="39030-200">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="39030-201">ASP.NET Core 3.0 之前的版本中，`JsonResult.SerializerSettings` 接受特定于 `Newtonsoft.Json` 的 `JsonSerializerSettings` 的实例。</span><span class="sxs-lookup"><span data-stu-id="39030-201">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="39030-202">生成 [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 文档。</span><span class="sxs-lookup"><span data-stu-id="39030-202">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="39030-203">可以使用 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` 配置基于 `Newtonsoft.Json` 的格式化程序的功能：</span><span class="sxs-lookup"><span data-stu-id="39030-203">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="39030-204">可以使用 `JsonResult` 配置基于每个操作的输出序列化选项。</span><span class="sxs-lookup"><span data-stu-id="39030-204">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="39030-205">例如：</span><span class="sxs-lookup"><span data-stu-id="39030-205">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="39030-206">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="39030-206">Add XML format support</span></span>

<span data-ttu-id="39030-207">XML 格式需要 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="39030-207">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="39030-208">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> 来配置使用 <xref:System.Xml.Serialization.XmlSerializer> 实现的 XML 格式化程序：</span><span class="sxs-lookup"><span data-stu-id="39030-208">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="39030-209">前面的代码将使用 `XmlSerializer` 将结果序列化。</span><span class="sxs-lookup"><span data-stu-id="39030-209">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="39030-210">使用前面的代码时，控制器方法应基于请求的 `Accept` 标头返回相应的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-210">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="39030-211">指定格式</span><span class="sxs-lookup"><span data-stu-id="39030-211">Specify a format</span></span>

<span data-ttu-id="39030-212">若要限制响应格式，请应用 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 筛选器。</span><span class="sxs-lookup"><span data-stu-id="39030-212">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="39030-213">与大多数[筛选器](xref:mvc/controllers/filters)一样， `[Produces]` 可以在操作、控制器或全局范围内应用：</span><span class="sxs-lookup"><span data-stu-id="39030-213">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="39030-214">上述 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 筛选器：</span><span class="sxs-lookup"><span data-stu-id="39030-214">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="39030-215">强制控制器内的所有操作返回 JSON 格式的响应。</span><span class="sxs-lookup"><span data-stu-id="39030-215">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="39030-216">若已配置其他格式化程序，并且客户端指定了其他格式，将返回 JSON。</span><span class="sxs-lookup"><span data-stu-id="39030-216">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="39030-217">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)。</span><span class="sxs-lookup"><span data-stu-id="39030-217">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="39030-218">特例格式化程序</span><span class="sxs-lookup"><span data-stu-id="39030-218">Special case formatters</span></span>

<span data-ttu-id="39030-219">一些特例是使用内置格式化程序实现的。</span><span class="sxs-lookup"><span data-stu-id="39030-219">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="39030-220">默认情况下，`string` 返回类型的格式将设为 text/plain（如果通过 `Accept` 标头请求则为 text/html）\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="39030-220">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="39030-221">可以通过删除 <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="39030-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="39030-222">在 `ConfigureServices` 方法中删除格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-222">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="39030-223">有模型对象返回类型的操作将在返回 `null` 时返回 `204 No Content`。</span><span class="sxs-lookup"><span data-stu-id="39030-223">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="39030-224">可以通过删除 <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="39030-224">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="39030-225">以下代码删除 `StringOutputFormatter` 和 `HttpNoContentOutputFormatter`。</span><span class="sxs-lookup"><span data-stu-id="39030-225">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="39030-226">如果没有 `StringOutputFormatter`，内置 JSON 格式化程序将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-226">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="39030-227">如果删除了内置 JSON 格式化程序并提供了 XML 格式化程序，则 XML 格式化程序将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="39030-227">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="39030-228">否则，`string` 返回类型返回 `406 Not Acceptable`。</span><span class="sxs-lookup"><span data-stu-id="39030-228">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="39030-229">没有 `HttpNoContentOutputFormatter`，null 对象将使用配置的格式化程序来进行格式设置。</span><span class="sxs-lookup"><span data-stu-id="39030-229">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="39030-230">例如：</span><span class="sxs-lookup"><span data-stu-id="39030-230">For example:</span></span>

* <span data-ttu-id="39030-231">JSON 格式化程序返回正文为 `null` 的响应。</span><span class="sxs-lookup"><span data-stu-id="39030-231">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="39030-232">设置属性 `xsi:nil="true"` 时，XML 格式化程序返回空 XML 元素。</span><span class="sxs-lookup"><span data-stu-id="39030-232">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="39030-233">响应格式 URL 映射</span><span class="sxs-lookup"><span data-stu-id="39030-233">Response format URL mappings</span></span>

<span data-ttu-id="39030-234">客户端可以在 URL 中请求特定格式，例如：</span><span class="sxs-lookup"><span data-stu-id="39030-234">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="39030-235">在查询字符串中，或在路径中。</span><span class="sxs-lookup"><span data-stu-id="39030-235">In the query string or part of the path.</span></span>
* <span data-ttu-id="39030-236">使用格式特定的文件扩展名，如 .xml 或 .json。</span><span class="sxs-lookup"><span data-stu-id="39030-236">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="39030-237">请求路径的映射必须在 API 使用的路由中指定。</span><span class="sxs-lookup"><span data-stu-id="39030-237">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="39030-238">例如：</span><span class="sxs-lookup"><span data-stu-id="39030-238">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="39030-239">上述路由将允许指定所请求格式为可选文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="39030-239">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="39030-240">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)特性检查中的格式值是否存在 `RouteData` ，并在创建响应时将响应格式映射到适当的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="39030-240">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="39030-241">路由</span><span class="sxs-lookup"><span data-stu-id="39030-241">Route</span></span>        |             <span data-ttu-id="39030-242">格式化程序</span><span class="sxs-lookup"><span data-stu-id="39030-242">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="39030-243">默认输出格式化程序</span><span class="sxs-lookup"><span data-stu-id="39030-243">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="39030-244">JSON 格式化程序（如配置）</span><span class="sxs-lookup"><span data-stu-id="39030-244">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="39030-245">XML 格式化程序（如配置）</span><span class="sxs-lookup"><span data-stu-id="39030-245">The XML formatter (if configured)</span></span>  |
