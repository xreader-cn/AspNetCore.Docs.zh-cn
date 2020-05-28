---
<span data-ttu-id="f8482-101">标题：作者：说明：毫秒。作者： ms. 自定义：毫秒：非 loc：</span><span class="sxs-lookup"><span data-stu-id="f8482-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f8482-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f8482-102">'Blazor'</span></span>
- <span data-ttu-id="f8482-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f8482-103">'Identity'</span></span>
- <span data-ttu-id="f8482-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f8482-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f8482-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f8482-105">'Razor'</span></span>
- <span data-ttu-id="f8482-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="f8482-106">'SignalR' uid:</span></span> 

---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="f8482-107">在 ASP.NET Core 中启用跨域请求（CORS）</span><span class="sxs-lookup"><span data-stu-id="f8482-107">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8482-108">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f8482-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="f8482-109">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-109">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="f8482-110">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-110">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="f8482-111">此限制称为同域策略  。</span><span class="sxs-lookup"><span data-stu-id="f8482-111">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="f8482-112">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="f8482-112">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="f8482-113">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-113">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="f8482-114">有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="f8482-114">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="f8482-115">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="f8482-115">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="f8482-116">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-116">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="f8482-117">**不**是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="f8482-117">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="f8482-118">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="f8482-118">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="f8482-119">有关详细信息，请参阅[CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="f8482-119">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="f8482-120">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-120">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="f8482-121">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="f8482-121">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="f8482-122">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f8482-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="f8482-123">同一原点</span><span class="sxs-lookup"><span data-stu-id="f8482-123">Same origin</span></span>

<span data-ttu-id="f8482-124">如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="f8482-124">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="f8482-125">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="f8482-125">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="f8482-126">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="f8482-126">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="f8482-127">`https://example.net`：不同的域</span><span class="sxs-lookup"><span data-stu-id="f8482-127">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="f8482-128">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="f8482-128">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="f8482-129">`http://example.com/foo.html`：不同方案</span><span class="sxs-lookup"><span data-stu-id="f8482-129">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="f8482-130">`https://example.com:9000/foo.html`：不同的端口</span><span class="sxs-lookup"><span data-stu-id="f8482-130">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="f8482-131">启用 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-131">Enable CORS</span></span>

<span data-ttu-id="f8482-132">有三种方法可启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="f8482-132">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="f8482-133">使用[命名策略](#np)或[默认策略](#dp)的中间件。</span><span class="sxs-lookup"><span data-stu-id="f8482-133">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="f8482-134">使用[终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="f8482-134">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="f8482-135">带有[[EnableCors]](#attr)属性的。</span><span class="sxs-lookup"><span data-stu-id="f8482-135">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="f8482-136">通过命名策略使用[[EnableCors]](#attr)属性，可在限制支持 CORS 的终结点时提供最佳控制。</span><span class="sxs-lookup"><span data-stu-id="f8482-136">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="f8482-137">以下各节详细介绍了每种方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-137">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="f8482-138">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-138">CORS with named policy and middleware</span></span>

<span data-ttu-id="f8482-139">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-139">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="f8482-140">以下代码将 CORS 策略应用到具有指定来源的所有应用的终结点：</span><span class="sxs-lookup"><span data-stu-id="f8482-140">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="f8482-141">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="f8482-141">The preceding code:</span></span>

* <span data-ttu-id="f8482-142">将策略名称设置为 `_myAllowSpecificOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-142">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="f8482-143">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="f8482-143">The policy name is arbitrary.</span></span>
* <span data-ttu-id="f8482-144">调用 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 扩展方法并指定 `_myAllowSpecificOrigins` CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-144">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="f8482-145">`UseCors`添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="f8482-145">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="f8482-146">必须将对的调用 `UseCors` 置于之后 `UseRouting` 但在之前 `UseAuthorization` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-146">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="f8482-147">有关详细信息，请参阅[中间件顺序](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="f8482-147">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="f8482-148"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="f8482-148">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f8482-149">Lambda 采用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 对象。</span><span class="sxs-lookup"><span data-stu-id="f8482-149">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="f8482-150">本文稍后将介绍[配置选项](#cors-policy-options)，如 `WithOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-150">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="f8482-151">启用 `_myAllowSpecificOrigins` 所有控制器终结点的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-151">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="f8482-152">请参阅[终结点路由](#ecors)，将 CORS 策略应用到特定终结点。</span><span class="sxs-lookup"><span data-stu-id="f8482-152">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="f8482-153">通过终结点路由，CORS 中间件**必须**配置为在对和的调用之间执行 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="f8482-154">有关测试代码的说明，请参阅[测试 CORS](#testc) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="f8482-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="f8482-155"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="f8482-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="f8482-156">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="f8482-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="f8482-157">这些 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 方法可以链接在一起，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="f8482-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="f8482-158">注意：指定的 URL**不**能包含尾随斜杠（ `/` ）。</span><span class="sxs-lookup"><span data-stu-id="f8482-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="f8482-159">如果 URL 以结尾 `/` ，则比较返回， `false` 不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="f8482-160">具有默认策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="f8482-161">以下突出显示的代码将启用默认 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="f8482-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="f8482-162">前面的代码将默认的 CORS 策略应用到所有控制器终结点。</span><span class="sxs-lookup"><span data-stu-id="f8482-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="f8482-163">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="f8482-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="f8482-164">目前使用每个终结点启用 CORS 不 `RequireCors` 支持[自动预检请求](#apf)。 **not**</span><span class="sxs-lookup"><span data-stu-id="f8482-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="f8482-165">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/20709)和[测试与终结点路由和 [HTTPOPTIONS] 的 CORS](#tcer)。</span><span class="sxs-lookup"><span data-stu-id="f8482-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="f8482-166">使用终结点路由，可以使用一组扩展方法在每个终结点上启用 CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="f8482-167">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="f8482-167">In the preceding code:</span></span>

* <span data-ttu-id="f8482-168">`app.UseCors`启用 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="f8482-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="f8482-169">由于尚未配置默认策略，因此 `app.UseCors()` 单独不启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="f8482-170">`/echo`和控制器端点允许使用指定策略的跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="f8482-171">`/echo2`和 Razor Pages 终结点**不**允许跨源请求，因为未指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="f8482-172">[[DisableCors]](#dc)特性**不**会禁用通过终结点路由启用的 CORS `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="f8482-173">请参阅[测试与终结点路由和 [HttpOptions] 的 CORS](#tcer) ，获取与前面类似的代码测试说明。</span><span class="sxs-lookup"><span data-stu-id="f8482-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="f8482-174">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-174">Enable CORS with attributes</span></span>

<span data-ttu-id="f8482-175">使用[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性启用 CORS，并将命名策略应用到只有那些需要 CORS 的终结点提供了精细的控制。</span><span class="sxs-lookup"><span data-stu-id="f8482-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="f8482-176">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="f8482-177">`[EnableCors]`特性启用所选终结点的 CORS，而不是所有终结点：</span><span class="sxs-lookup"><span data-stu-id="f8482-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="f8482-178">`[EnableCors]`指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="f8482-179">`[EnableCors("{Policy String}")]`指定命名策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="f8482-180">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="f8482-180">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="f8482-181">分页`PageModel`</span><span class="sxs-lookup"><span data-stu-id="f8482-181"> Page `PageModel`</span></span>
* <span data-ttu-id="f8482-182">控制器</span><span class="sxs-lookup"><span data-stu-id="f8482-182">Controller</span></span>
* <span data-ttu-id="f8482-183">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="f8482-183">Controller action method</span></span>

<span data-ttu-id="f8482-184">可以将不同的策略应用到具有属性的控制器、页面模型或操作方法 `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="f8482-185">如果将 `[EnableCors]` 属性应用于控制器、页面模型或操作方法，并在中间件中启用了 CORS，则会应用**这两种**策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="f8482-186">**建议不要结合策略。使用** `[EnableCors]` **特性或中间件，而不是在同一应用中。**</span><span class="sxs-lookup"><span data-stu-id="f8482-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="f8482-187">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="f8482-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="f8482-188">下面的代码创建两个 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="f8482-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="f8482-189">对于限制 CORS 请求的最佳控制：</span><span class="sxs-lookup"><span data-stu-id="f8482-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="f8482-190">`[EnableCors("MyPolicy")]`与命名策略一起使用。</span><span class="sxs-lookup"><span data-stu-id="f8482-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="f8482-191">不要定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-191">Don't define a default policy.</span></span>
* <span data-ttu-id="f8482-192">请勿使用[终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="f8482-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="f8482-193">下一节中的代码满足前面的列表。</span><span class="sxs-lookup"><span data-stu-id="f8482-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="f8482-194">有关测试代码的说明，请参阅[测试 CORS](#testc) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="f8482-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="f8482-195">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-195">Disable CORS</span></span>

<span data-ttu-id="f8482-196">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **特性不会禁用已**通过[终结点路由](#ecors)启用的 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="f8482-197">以下代码定义 CORS 策略 `"MyPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="f8482-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="f8482-198">以下代码禁用操作的 CORS `GetValues2` ：</span><span class="sxs-lookup"><span data-stu-id="f8482-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="f8482-199">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="f8482-199">The preceding code:</span></span>

* <span data-ttu-id="f8482-200">不会使用[终结点路由](#ecors)启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="f8482-201">不定义[默认的 CORS 策略](#dp)。</span><span class="sxs-lookup"><span data-stu-id="f8482-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="f8482-202">使用[[EnableCors （"MyPolicy"）]](#attr)启用控制器的 `"MyPolicy"` CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="f8482-203">为方法禁用 CORS `GetValues2` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="f8482-204">有关测试上述代码的说明，请参阅[测试 CORS](#testc) 。</span><span class="sxs-lookup"><span data-stu-id="f8482-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="f8482-205">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="f8482-205">CORS policy options</span></span>

<span data-ttu-id="f8482-206">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="f8482-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="f8482-207">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="f8482-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="f8482-208">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="f8482-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="f8482-209">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="f8482-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="f8482-210">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="f8482-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="f8482-211">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="f8482-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="f8482-212">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="f8482-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="f8482-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在中调用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8482-214">对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="f8482-215">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="f8482-215">Set the allowed origins</span></span>

<span data-ttu-id="f8482-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允许所有来源的 CORS 请求和任何方案（ `http` 或 `https` ）。</span><span class="sxs-lookup"><span data-stu-id="f8482-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="f8482-217">`AllowAnyOrigin`是不安全的，因为*任何网站*都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="f8482-218">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="f8482-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="f8482-219">使用这两种方法配置应用时，CORS 服务将返回无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="f8482-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="f8482-220">`AllowAnyOrigin`影响预检请求和 `Access-Control-Allow-Origin` 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="f8482-221">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="f8482-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：将 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 策略的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="f8482-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="f8482-223">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="f8482-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="f8482-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="f8482-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="f8482-225">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="f8482-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="f8482-226">影响预检请求和 `Access-Control-Allow-Methods` 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="f8482-227">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="f8482-228">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="f8482-228">Set the allowed request headers</span></span>

<span data-ttu-id="f8482-229">若要允许在 CORS 请求中发送特定标头（称为[作者请求标头](https://xhr.spec.whatwg.org/#request)），请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="f8482-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="f8482-230">若要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="f8482-231">`AllowAnyHeader`影响预检请求和[访问控制请求标](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)头。</span><span class="sxs-lookup"><span data-stu-id="f8482-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="f8482-232">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="f8482-233">`WithHeaders`仅当发送的标头 `Access-Control-Request-Headers` 与中所述的标头完全匹配时，才可以使用 CORS 中间件策略匹配指定的特定标头 `WithHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="f8482-234">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="f8482-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="f8482-235">CORS 中间件使用以下请求标头拒绝预检请求，因为 `Content-Language` （[HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)）未在中列出 `WithHeaders` ：</span><span class="sxs-lookup"><span data-stu-id="f8482-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="f8482-236">应用返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="f8482-237">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="f8482-238">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="f8482-238">Set the exposed response headers</span></span>

<span data-ttu-id="f8482-239">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="f8482-240">有关详细信息，请参阅[W3C 跨域资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="f8482-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="f8482-241">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="f8482-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="f8482-242">CORS 规范将这些标头称为*简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="f8482-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="f8482-243">若要使其他标头可用于应用程序，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="f8482-244">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="f8482-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="f8482-245">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="f8482-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="f8482-246">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="f8482-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="f8482-247">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="f8482-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="f8482-248">若要使用跨域请求发送凭据，客户端必须设置 `XMLHttpRequest.withCredentials` 为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="f8482-249">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="f8482-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="f8482-250">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="f8482-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="f8482-251">使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="f8482-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="f8482-252">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="f8482-252">The server must allow the credentials.</span></span> <span data-ttu-id="f8482-253">若要允许跨域凭据，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="f8482-254">HTTP 响应包含一个 `Access-Control-Allow-Credentials` 标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="f8482-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="f8482-255">如果浏览器发送凭据，但响应不包含有效的 `Access-Control-Allow-Credentials` 标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f8482-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="f8482-256">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="f8482-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="f8482-257">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="f8482-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="f8482-258">CORS 规范还指出， `"*"` 如果标头存在，则将 "源" 设置为 "（所有源）" 无效 `Access-Control-Allow-Credentials` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="f8482-259">预检请求</span><span class="sxs-lookup"><span data-stu-id="f8482-259">Preflight requests</span></span>

<span data-ttu-id="f8482-260">对于某些 CORS 请求，浏览器会在发出实际请求之前发送额外的[OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="f8482-261">此请求称为[预检请求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。</span><span class="sxs-lookup"><span data-stu-id="f8482-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="f8482-262">如果满足以下**所有**条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="f8482-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="f8482-263">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="f8482-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="f8482-264">应用不会设置、、、或以外的请求标头 `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="f8482-265">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="f8482-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="f8482-266">为客户端请求设置的请求标头上的规则适用于应用通过在对象上调用来设置的标头 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="f8482-267">CORS 规范调用这些标头[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="f8482-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="f8482-268">此规则不适用于浏览器可以设置的标头，如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="f8482-269">下面是一个示例响应，它类似于在本文档的 "[测试 CORS](#testc) " 部分中通过 " **Put test** " 按钮发出的预检请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="f8482-270">预检请求使用[HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="f8482-271">它可能包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="f8482-271">It may include the following headers:</span></span>

* <span data-ttu-id="f8482-272">[访问控制-请求-方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="f8482-273">[访问控制-请求标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="f8482-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="f8482-274">如前文所述，这不包含浏览器设置的标头，如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="f8482-275">访问控制-允许-方法</span><span class="sxs-lookup"><span data-stu-id="f8482-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="f8482-276">如果预检请求被拒绝，应用将返回响应， `200 OK` 但不会设置 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="f8482-277">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="f8482-278">有关拒绝的预检请求的示例，请参阅本文档的[测试 CORS](#testc)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="f8482-279">使用 F12 工具时，控制台应用会显示类似于以下内容之一的错误，具体取决于浏览器：</span><span class="sxs-lookup"><span data-stu-id="f8482-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="f8482-280">Firefox：跨源请求被阻止：相同的源策略不允许读取上的远程资源 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="f8482-281">（原因： CORS 请求未成功）。</span><span class="sxs-lookup"><span data-stu-id="f8482-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="f8482-282">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="f8482-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="f8482-283">基于 Chromium：派生自源 "" 的 "" 访问权限已被 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS 策略阻止：响应预检请求未通过访问控制检查：请求的资源上没有 "访问控制-允许-源" 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="f8482-284">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="f8482-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="f8482-285">若要允许特定标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="f8482-286">若要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="f8482-287">浏览器的设置方式并不一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="f8482-288">如果：</span><span class="sxs-lookup"><span data-stu-id="f8482-288">If either:</span></span>

* <span data-ttu-id="f8482-289">标头设置为以外的任何内容`"*"`</span><span class="sxs-lookup"><span data-stu-id="f8482-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="f8482-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>调用：至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="f8482-291">自动预检请求代码</span><span class="sxs-lookup"><span data-stu-id="f8482-291">Automatic preflight request code</span></span>

<span data-ttu-id="f8482-292">应用 CORS 策略的时间：</span><span class="sxs-lookup"><span data-stu-id="f8482-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="f8482-293">通过 `app.UseCors` 在中调用 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="f8482-294">使用 `[EnableCors]` 特性。</span><span class="sxs-lookup"><span data-stu-id="f8482-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="f8482-295">ASP.NET Core 对 "预检选项" 请求做出响应。</span><span class="sxs-lookup"><span data-stu-id="f8482-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="f8482-296">目前使用每个终结点启用 CORS 不 `RequireCors` 支持自动**not**预检请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="f8482-297">本文档的 "[测试 CORS](#testc) " 部分说明了此行为。</span><span class="sxs-lookup"><span data-stu-id="f8482-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="f8482-298">用于预检请求的 [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="f8482-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="f8482-299">如果为 CORS 启用了适当的策略，ASP.NET Core 通常会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="f8482-300">在某些情况下，可能不会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="f8482-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="f8482-301">例如，将[CORS 用于终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="f8482-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="f8482-302">下面的代码使用[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)特性为 OPTIONS 请求创建终结点：</span><span class="sxs-lookup"><span data-stu-id="f8482-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="f8482-303">有关测试上述代码的说明，请参阅[通过终结点路由测试 CORS 和 [HttpOptions]](#tcer) 。</span><span class="sxs-lookup"><span data-stu-id="f8482-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="f8482-304">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="f8482-304">Set the preflight expiration time</span></span>

<span data-ttu-id="f8482-305">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="f8482-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="f8482-306">若要设置此标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="f8482-307">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="f8482-307">How CORS works</span></span>

<span data-ttu-id="f8482-308">本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="f8482-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="f8482-309">CORS**不**是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="f8482-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="f8482-310">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="f8482-311">例如，恶意执行组件可能对站点使用[跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求来窃取信息。</span><span class="sxs-lookup"><span data-stu-id="f8482-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="f8482-312">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="f8482-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="f8482-313">它由客户端（浏览器）来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="f8482-314">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="f8482-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="f8482-315">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="f8482-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="f8482-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="f8482-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="f8482-317">Postman</span><span class="sxs-lookup"><span data-stu-id="f8482-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="f8482-318">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="f8482-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="f8482-319">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="f8482-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="f8482-320">这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="f8482-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="f8482-321">没有 CORS 的浏览器不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="f8482-322">在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="f8482-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="f8482-323">JSONP 不使用 XHR，而是使用 `<script>` 标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="f8482-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="f8482-324">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="f8482-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="f8482-325">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="f8482-326">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="f8482-327">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="f8482-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="f8482-328">部署的[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的 " [PUT 测试" 按钮](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="f8482-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="f8482-329">下面是一个从 "[值](https://cors3.azurewebsites.net/)" 测试按钮到的跨源请求的示例 `https://cors1.azurewebsites.net/api/values` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="f8482-330">`Origin`标头：</span><span class="sxs-lookup"><span data-stu-id="f8482-330">The `Origin` header:</span></span>

* <span data-ttu-id="f8482-331">提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="f8482-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="f8482-332">是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="f8482-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="f8482-333">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="f8482-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="f8482-334">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="f8482-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="f8482-335">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="f8482-335">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="f8482-336">在 `OPTIONS` 请求中，服务器设置响应中的**响应标头** `Access-Control-Allow-Origin: {allowed origin}` 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="f8482-337">例如，已部署的[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` 请求包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="f8482-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="f8482-338">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="f8482-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="f8482-339">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="f8482-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="f8482-340">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="f8482-340">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="f8482-341">在前面的**响应标头**中，服务器设置响应中的[访问控制允许源](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="f8482-342">`https://cors1.azurewebsites.net`此标头的值与 `Origin` 请求中的标头相匹配。</span><span class="sxs-lookup"><span data-stu-id="f8482-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="f8482-343">如果 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 调用了，则将 `Access-Control-Allow-Origin: *` 返回通配符值。</span><span class="sxs-lookup"><span data-stu-id="f8482-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="f8482-344">`AllowAnyOrigin`允许任何源。</span><span class="sxs-lookup"><span data-stu-id="f8482-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="f8482-345">如果响应不包含 `Access-Control-Allow-Origin` 标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f8482-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="f8482-346">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="f8482-347">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8482-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="f8482-348">显示选项请求</span><span class="sxs-lookup"><span data-stu-id="f8482-348">Display OPTIONS requests</span></span>

<span data-ttu-id="f8482-349">默认情况下，Chrome 和 Edge 浏览器不会在 F12 工具的 "网络" 选项卡上显示 "请求" 选项。</span><span class="sxs-lookup"><span data-stu-id="f8482-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="f8482-350">若要在这些浏览器中显示选项请求：</span><span class="sxs-lookup"><span data-stu-id="f8482-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="f8482-351">`chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="f8482-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="f8482-352">禁用标志。</span><span class="sxs-lookup"><span data-stu-id="f8482-352">disable the flag.</span></span>
* <span data-ttu-id="f8482-353">重新启动.</span><span class="sxs-lookup"><span data-stu-id="f8482-353">restart.</span></span>

<span data-ttu-id="f8482-354">默认情况下，Firefox 显示 "选项请求"。</span><span class="sxs-lookup"><span data-stu-id="f8482-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="f8482-355">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-355">CORS in IIS</span></span>

<span data-ttu-id="f8482-356">部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="f8482-357">若要支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。</span><span class="sxs-lookup"><span data-stu-id="f8482-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="f8482-358">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-358">Test CORS</span></span>

<span data-ttu-id="f8482-359">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)包含测试 CORS 的代码。</span><span class="sxs-lookup"><span data-stu-id="f8482-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="f8482-360">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="f8482-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="f8482-361">该示例是一个 API 项目，其中 Razor 添加了页面：</span><span class="sxs-lookup"><span data-stu-id="f8482-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="f8482-362">`WithOrigins("https://localhost:<port>");`应仅用于测试示例应用程序，类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="f8482-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="f8482-363">下面 `ValuesController` 提供用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="f8482-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="f8482-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)由[RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 包提供，并显示路由信息。</span><span class="sxs-lookup"><span data-stu-id="f8482-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="f8482-365">使用以下方法之一测试前面的示例代码：</span><span class="sxs-lookup"><span data-stu-id="f8482-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="f8482-366">使用部署的示例应用 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 。</span><span class="sxs-lookup"><span data-stu-id="f8482-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="f8482-367">无需下载示例。</span><span class="sxs-lookup"><span data-stu-id="f8482-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="f8482-368">`dotnet run`使用的默认 URL 运行示例 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="f8482-369">从 Visual Studio 运行示例，并将端口设置为44398，查找的 URL `https://localhost:44398` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="f8482-370">使用带有 F12 工具的浏览器：</span><span class="sxs-lookup"><span data-stu-id="f8482-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="f8482-371">选择 "**值**" 按钮，然后查看 "**网络**" 选项卡中的标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="f8482-372">选择 "**放置测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="f8482-372">Select the **PUT test** button.</span></span> <span data-ttu-id="f8482-373">请参阅[显示选项请求](#options)，以获取有关显示选项请求的说明。</span><span class="sxs-lookup"><span data-stu-id="f8482-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="f8482-374">**PUT 测试**创建两个请求：一个选项预检请求和 PUT 请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="f8482-375">选择此 **`GetValues2 [DisableCors]`** 按钮可触发失败的 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="f8482-376">如文档中所述，响应返回200成功，但不进行 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="f8482-377">选择 "**控制台**" 选项卡以查看 CORS 错误。</span><span class="sxs-lookup"><span data-stu-id="f8482-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="f8482-378">根据浏览器，将显示类似于以下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="f8482-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="f8482-379">`'https://cors1.azurewebsites.net/api/values/GetValues2'`CORS 策略已阻止从原始位置获取的访问权限 `'https://cors3.azurewebsites.net'` ：请求的资源上没有 "访问控制-允许" 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="f8482-380">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="f8482-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="f8482-381">可以使用[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)等工具来测试启用了[CORS 的终结](https://curl.haxx.se/)点。</span><span class="sxs-lookup"><span data-stu-id="f8482-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="f8482-382">使用工具时，标头指定的请求源 `Origin` 必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="f8482-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="f8482-383">如果请求不是基于标头值*跨*域的，则 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="f8482-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="f8482-384">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="f8482-385">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="f8482-386">以下命令使用 `curl` 发出带有以下信息的选项请求：</span><span class="sxs-lookup"><span data-stu-id="f8482-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="f8482-387">通过终结点路由和 [HttpOptions] 测试 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="f8482-388">目前使用每个终结点启用 CORS 不 `RequireCors` 支持[自动预检请求](#apf)。 **not**</span><span class="sxs-lookup"><span data-stu-id="f8482-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="f8482-389">请考虑以下代码，它使用[终结点路由启用 CORS](#ecors)：</span><span class="sxs-lookup"><span data-stu-id="f8482-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="f8482-390">下面 `TodoItems1Controller` 提供用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="f8482-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="f8482-391">从已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[测试页](https://cors1.azurewebsites.net/test?number=1)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="f8482-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="f8482-392">**Delete [EnableCors]** 和**GET [EnableCors]** 按钮成功，因为终结点具有 `[EnableCors]` 和响应预检请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="f8482-393">其他终结点失败。</span><span class="sxs-lookup"><span data-stu-id="f8482-393">The other endpoints fails.</span></span> <span data-ttu-id="f8482-394">"**获取**" 按钮失败，因为[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)发送：</span><span class="sxs-lookup"><span data-stu-id="f8482-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="f8482-395">下面 `TodoItems2Controller` 提供了类似的终结点，但包含响应选项请求的显式代码：</span><span class="sxs-lookup"><span data-stu-id="f8482-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="f8482-396">从已部署示例的[测试页](https://cors1.azurewebsites.net/test?number=2)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="f8482-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="f8482-397">在 "**控制器**" 下拉列表中，选择 "**预检**"，然后**设置 "控制器**"。</span><span class="sxs-lookup"><span data-stu-id="f8482-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="f8482-398">对终结点的所有 CORS 调用都将 `TodoItems2Controller` 成功。</span><span class="sxs-lookup"><span data-stu-id="f8482-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8482-399">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8482-399">Additional resources</span></span>

* [<span data-ttu-id="f8482-400">跨域资源共享（CORS）</span><span class="sxs-lookup"><span data-stu-id="f8482-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="f8482-401">IIS CORS 模块入门</span><span class="sxs-lookup"><span data-stu-id="f8482-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8482-402">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8482-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f8482-403">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="f8482-404">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="f8482-405">此限制称为同域策略  。</span><span class="sxs-lookup"><span data-stu-id="f8482-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="f8482-406">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="f8482-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="f8482-407">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="f8482-408">有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="f8482-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="f8482-409">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="f8482-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="f8482-410">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="f8482-411">**不**是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="f8482-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="f8482-412">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="f8482-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="f8482-413">有关详细信息，请参阅[CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="f8482-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="f8482-414">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="f8482-415">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="f8482-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="f8482-416">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f8482-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="f8482-417">同一原点</span><span class="sxs-lookup"><span data-stu-id="f8482-417">Same origin</span></span>

<span data-ttu-id="f8482-418">如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="f8482-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="f8482-419">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="f8482-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="f8482-420">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="f8482-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="f8482-421">`https://example.net`：不同的域</span><span class="sxs-lookup"><span data-stu-id="f8482-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="f8482-422">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="f8482-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="f8482-423">`http://example.com/foo.html`：不同方案</span><span class="sxs-lookup"><span data-stu-id="f8482-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="f8482-424">`https://example.com:9000/foo.html`：不同的端口</span><span class="sxs-lookup"><span data-stu-id="f8482-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="f8482-425">比较来源时，Internet Explorer 不会考虑该端口。</span><span class="sxs-lookup"><span data-stu-id="f8482-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="f8482-426">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="f8482-427">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="f8482-428">以下代码通过指定源为整个应用启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="f8482-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="f8482-429">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="f8482-429">The preceding code:</span></span>

* <span data-ttu-id="f8482-430">将策略名称设置为 " \_ myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="f8482-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="f8482-431">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="f8482-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="f8482-432">调用 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 扩展方法，该方法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="f8482-433"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="f8482-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f8482-434">Lambda 采用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 对象。</span><span class="sxs-lookup"><span data-stu-id="f8482-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="f8482-435">本文稍后将介绍[配置选项](#cors-policy-options)，如 `WithOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="f8482-436"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="f8482-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="f8482-437">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="f8482-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="f8482-438"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以链接方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="f8482-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="f8482-439">注意： URL**不得包含尾随**斜杠（ `/` ）。</span><span class="sxs-lookup"><span data-stu-id="f8482-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="f8482-440">如果 URL 以结尾 `/` ，则比较返回， `false` 不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="f8482-441">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="f8482-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="f8482-442">注意： `UseCors` 必须先调用 `UseMvc` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="f8482-443">请参阅[在页面 Razor 、控制器和操作方法中启用 cors，](#ecors)以在页面/控制器/操作级别应用 cors 策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="f8482-444">有关测试代码的说明，请参阅[测试 CORS](#test) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="f8482-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="f8482-445">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-445">Enable CORS with attributes</span></span>

<span data-ttu-id="f8482-446">[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="f8482-447">`[EnableCors]`属性为所选终结点启用 CORS，而不是所有终结点。</span><span class="sxs-lookup"><span data-stu-id="f8482-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="f8482-448">使用 `[EnableCors]` 指定默认策略并 `[EnableCors("{Policy String}")]` 指定策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="f8482-449">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="f8482-449">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="f8482-450">分页`PageModel`</span><span class="sxs-lookup"><span data-stu-id="f8482-450"> Page `PageModel`</span></span>
* <span data-ttu-id="f8482-451">控制器</span><span class="sxs-lookup"><span data-stu-id="f8482-451">Controller</span></span>
* <span data-ttu-id="f8482-452">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="f8482-452">Controller action method</span></span>

<span data-ttu-id="f8482-453">您可以使用属性将不同的策略应用到控制器/页模型/操作 `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="f8482-454">如果将 `[EnableCors]` 属性应用于控制器/页面模型/操作方法，并在中间件中启用了 CORS，则会应用**这两种**策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="f8482-455">建议**不要**合并策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="f8482-456">使用 `[EnableCors]` 特性或中间件，**不能同时使用两者**。</span><span class="sxs-lookup"><span data-stu-id="f8482-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="f8482-457">使用时 `[EnableCors]` ，请**不要**定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="f8482-458">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="f8482-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="f8482-459">以下代码创建 CORS 默认策略和名为的策略 `"AnotherPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="f8482-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="f8482-460">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-460">Disable CORS</span></span>

<span data-ttu-id="f8482-461">[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性为控制器/页模型/操作禁用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="f8482-462">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="f8482-462">CORS policy options</span></span>

<span data-ttu-id="f8482-463">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="f8482-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="f8482-464">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="f8482-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="f8482-465">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="f8482-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="f8482-466">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="f8482-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="f8482-467">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="f8482-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="f8482-468">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="f8482-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="f8482-469">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="f8482-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="f8482-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在中调用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8482-471">对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="f8482-472">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="f8482-472">Set the allowed origins</span></span>

<span data-ttu-id="f8482-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允许所有来源的 CORS 请求和任何方案（ `http` 或 `https` ）。</span><span class="sxs-lookup"><span data-stu-id="f8482-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="f8482-474">`AllowAnyOrigin`是不安全的，因为*任何网站*都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="f8482-475">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="f8482-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="f8482-476">对于安全应用，如果客户端必须授权自身访问服务器资源，请指定准确的来源列表。</span><span class="sxs-lookup"><span data-stu-id="f8482-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="f8482-477">`AllowAnyOrigin`影响预检请求和 `Access-Control-Allow-Origin` 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="f8482-478">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="f8482-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：将 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 策略的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="f8482-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="f8482-480">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="f8482-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="f8482-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="f8482-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="f8482-482">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="f8482-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="f8482-483">影响预检请求和 `Access-Control-Allow-Methods` 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="f8482-484">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="f8482-485">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="f8482-485">Set the allowed request headers</span></span>

<span data-ttu-id="f8482-486">若要允许在 CORS 请求中发送特定标头（称为*作者请求标头*），请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="f8482-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="f8482-487">若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="f8482-488">此设置会影响预检请求和 `Access-Control-Request-Headers` 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="f8482-489">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="f8482-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="f8482-490">CORS 中间件始终允许发送四个标头， `Access-Control-Request-Headers` 而不考虑在 CorsPolicy 中配置的值。</span><span class="sxs-lookup"><span data-stu-id="f8482-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="f8482-491">此标头列表包括：</span><span class="sxs-lookup"><span data-stu-id="f8482-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="f8482-492">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="f8482-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="f8482-493">CORS 中间件使用以下请求标头成功响应了预检请求，因为 `Content-Language` 始终为白名单：</span><span class="sxs-lookup"><span data-stu-id="f8482-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="f8482-494">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="f8482-494">Set the exposed response headers</span></span>

<span data-ttu-id="f8482-495">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="f8482-496">有关详细信息，请参阅[W3C 跨域资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="f8482-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="f8482-497">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="f8482-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="f8482-498">CORS 规范将这些标头称为*简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="f8482-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="f8482-499">若要使其他标头可用于应用程序，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="f8482-500">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="f8482-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="f8482-501">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="f8482-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="f8482-502">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="f8482-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="f8482-503">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="f8482-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="f8482-504">若要使用跨域请求发送凭据，客户端必须设置 `XMLHttpRequest.withCredentials` 为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="f8482-505">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="f8482-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="f8482-506">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="f8482-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="f8482-507">使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="f8482-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="f8482-508">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="f8482-508">The server must allow the credentials.</span></span> <span data-ttu-id="f8482-509">若要允许跨域凭据，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="f8482-510">HTTP 响应包含一个 `Access-Control-Allow-Credentials` 标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="f8482-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="f8482-511">如果浏览器发送凭据，但响应不包含有效的 `Access-Control-Allow-Credentials` 标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f8482-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="f8482-512">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="f8482-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="f8482-513">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="f8482-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="f8482-514">CORS 规范还指出， `"*"` 如果标头存在，则将 "源" 设置为 "（所有源）" 无效 `Access-Control-Allow-Credentials` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="f8482-515">预检请求</span><span class="sxs-lookup"><span data-stu-id="f8482-515">Preflight requests</span></span>

<span data-ttu-id="f8482-516">对于某些 CORS 请求，浏览器会在发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="f8482-517">此请求称为*预检请求*。</span><span class="sxs-lookup"><span data-stu-id="f8482-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="f8482-518">如果满足以下条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="f8482-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="f8482-519">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="f8482-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="f8482-520">应用不会设置、、、或以外的请求标头 `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="f8482-521">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="f8482-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="f8482-522">为客户端请求设置的请求标头上的规则适用于应用通过在对象上调用来设置的标头 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="f8482-523">CORS 规范调用这些标头*作者请求标头*。</span><span class="sxs-lookup"><span data-stu-id="f8482-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="f8482-524">此规则不适用于浏览器可以设置的标头，如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="f8482-525">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="f8482-525">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="f8482-526">预航班请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="f8482-527">它包括两个特殊标头：</span><span class="sxs-lookup"><span data-stu-id="f8482-527">It includes two special headers:</span></span>

* <span data-ttu-id="f8482-528">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="f8482-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="f8482-529">`Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="f8482-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="f8482-530">如前文所述，这不包含浏览器设置的标头，如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="f8482-531">如果为 CORS 启用了适当的策略，ASP.NET Core 一般会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="f8482-532">请参阅[[HttpOptions] 属性以获取预检请求](#pro)。</span><span class="sxs-lookup"><span data-stu-id="f8482-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="f8482-533">CORS 预检请求可能包含一个 `Access-Control-Request-Headers` 标头，该标头向服务器指示与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="f8482-534">若要允许特定标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="f8482-535">若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="f8482-536">浏览器在设置方式上并不完全一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="f8482-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="f8482-537">如果将标头设置为 `"*"` （或使用）以外的任何内容 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ，则应该至少包含 `Accept` 、 `Content-Type` 、和 `Origin` ，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="f8482-538">下面是针对预检请求的示例响应（假定服务器允许该请求）：</span><span class="sxs-lookup"><span data-stu-id="f8482-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="f8482-539">响应包含一个 `Access-Control-Allow-Methods` 标头，其中列出了允许的方法，还可以选择 `Access-Control-Allow-Headers` 标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="f8482-540">如果预检请求成功，则浏览器发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="f8482-541">如果预检请求被拒绝，应用将返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="f8482-542">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="f8482-543">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="f8482-543">Set the preflight expiration time</span></span>

<span data-ttu-id="f8482-544">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="f8482-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="f8482-545">若要设置此标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="f8482-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="f8482-546">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="f8482-546">How CORS works</span></span>

<span data-ttu-id="f8482-547">本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="f8482-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="f8482-548">CORS**不**是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="f8482-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="f8482-549">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="f8482-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="f8482-550">例如，恶意执行组件可能会对站点使用[阻止跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求，以窃取信息。</span><span class="sxs-lookup"><span data-stu-id="f8482-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="f8482-551">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="f8482-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="f8482-552">它由客户端（浏览器）来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="f8482-553">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="f8482-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="f8482-554">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="f8482-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="f8482-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="f8482-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="f8482-556">Postman</span><span class="sxs-lookup"><span data-stu-id="f8482-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="f8482-557">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="f8482-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="f8482-558">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="f8482-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="f8482-559">这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="f8482-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="f8482-560">浏览器（不含 CORS）不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="f8482-561">在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="f8482-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="f8482-562">JSONP 不使用 XHR，而是使用 `<script>` 标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="f8482-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="f8482-563">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="f8482-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="f8482-564">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="f8482-565">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="f8482-566">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="f8482-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="f8482-567">下面是一个跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="f8482-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="f8482-568">`Origin`标头提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="f8482-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="f8482-569">`Origin`标头是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="f8482-569">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="f8482-570">如果服务器允许该请求，则会 `Access-Control-Allow-Origin` 在响应中设置标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="f8482-571">此标头的值可以与 `Origin` 请求中的标头相匹配，也可以是通配符值 `"*"` ，这意味着允许任何源：</span><span class="sxs-lookup"><span data-stu-id="f8482-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="f8482-572">如果响应不包含 `Access-Control-Allow-Origin` 标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="f8482-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="f8482-573">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="f8482-574">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8482-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="f8482-575">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-575">Test CORS</span></span>

<span data-ttu-id="f8482-576">测试 CORS：</span><span class="sxs-lookup"><span data-stu-id="f8482-576">To test CORS:</span></span>

1. <span data-ttu-id="f8482-577">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="f8482-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="f8482-578">或者，您也可以[下载该示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="f8482-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="f8482-579">使用本文档中的方法之一启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="f8482-580">例如：</span><span class="sxs-lookup"><span data-stu-id="f8482-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="f8482-581">`WithOrigins("https://localhost:<port>");`应仅用于测试示例应用程序，类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="f8482-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="f8482-582">创建 web 应用项目（ Razor 页或 MVC）。</span><span class="sxs-lookup"><span data-stu-id="f8482-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="f8482-583">该示例使用 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="f8482-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="f8482-584">可以在与 API 项目相同的解决方案中创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="f8482-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="f8482-585">将以下突出显示的代码添加到*索引 cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="f8482-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="f8482-586">在上面的代码中，将替换 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 为已部署应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="f8482-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="f8482-587">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="f8482-587">Deploy the API project.</span></span> <span data-ttu-id="f8482-588">例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="f8482-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="f8482-589">Razor从桌面运行页面或 MVC 应用，并单击 "**测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="f8482-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="f8482-590">使用 F12 工具查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="f8482-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="f8482-591">从中删除 localhost 源 `WithOrigins` 并部署应用。</span><span class="sxs-lookup"><span data-stu-id="f8482-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="f8482-592">或者，使用其他端口运行客户端应用。</span><span class="sxs-lookup"><span data-stu-id="f8482-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="f8482-593">例如，在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="f8482-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="f8482-594">与客户端应用程序进行测试。</span><span class="sxs-lookup"><span data-stu-id="f8482-594">Test with the client app.</span></span> <span data-ttu-id="f8482-595">CORS 故障返回一个错误，但错误消息不能用于 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="f8482-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="f8482-596">使用 F12 工具中的 "控制台" 选项卡查看错误。</span><span class="sxs-lookup"><span data-stu-id="f8482-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="f8482-597">根据浏览器，你会收到类似于以下内容的错误（在 F12 工具控制台中）：</span><span class="sxs-lookup"><span data-stu-id="f8482-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="f8482-598">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="f8482-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="f8482-599">**SEC7120： [CORS] 源在 `https://localhost:44375` `https://localhost:44375` 跨域资源的访问控制允许源响应标头中找不到`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="f8482-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="f8482-600">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="f8482-600">Using Chrome:</span></span>

     <span data-ttu-id="f8482-601">**`https://webapi.azurewebsites.net/api/values/1`CORS 策略已阻止从原始位置访问 XMLHttpRequest `https://localhost:44375` ：请求的资源上没有 "访问控制-允许" 标头。**</span><span class="sxs-lookup"><span data-stu-id="f8482-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="f8482-602">可以使用工具（如[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)）测试启用 CORS 的终结点。</span><span class="sxs-lookup"><span data-stu-id="f8482-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="f8482-603">使用工具时，标头指定的请求源 `Origin` 必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="f8482-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="f8482-604">如果请求不是基于标头值*跨*域的，则 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="f8482-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="f8482-605">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="f8482-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="f8482-606">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="f8482-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="f8482-607">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="f8482-607">CORS in IIS</span></span>

<span data-ttu-id="f8482-608">部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。</span><span class="sxs-lookup"><span data-stu-id="f8482-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="f8482-609">若要支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。</span><span class="sxs-lookup"><span data-stu-id="f8482-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8482-610">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8482-610">Additional resources</span></span>

* [<span data-ttu-id="f8482-611">跨域资源共享（CORS）</span><span class="sxs-lookup"><span data-stu-id="f8482-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="f8482-612">IIS CORS 模块入门</span><span class="sxs-lookup"><span data-stu-id="f8482-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
