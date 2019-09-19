---
title: 启用 ASP.NET Core 中的跨域请求 (CORS)
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: security/cors
ms.openlocfilehash: a34b77ad799a00707048c923b82b48774ce91682
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108069"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="6d390-103">启用 ASP.NET Core 中的跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="6d390-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="6d390-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d390-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6d390-105">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d390-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="6d390-106">浏览器安全可以防止网页向其他域发送请求，而不是为网页提供服务。</span><span class="sxs-lookup"><span data-stu-id="6d390-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="6d390-107">此限制称为*相同源策略*。</span><span class="sxs-lookup"><span data-stu-id="6d390-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="6d390-108">同一源策略可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="6d390-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="6d390-109">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="6d390-110">有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="6d390-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="6d390-111">[跨域资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="6d390-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="6d390-112">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="6d390-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="6d390-113">**不**是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="6d390-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="6d390-114">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="6d390-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="6d390-115">有关详细信息，请参阅[CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="6d390-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="6d390-116">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="6d390-117">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="6d390-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="6d390-118">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6d390-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="6d390-119">同一原点</span><span class="sxs-lookup"><span data-stu-id="6d390-119">Same origin</span></span>

<span data-ttu-id="6d390-120">如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="6d390-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="6d390-121">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="6d390-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="6d390-122">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="6d390-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="6d390-123">`https://example.net`&ndash;不同域</span><span class="sxs-lookup"><span data-stu-id="6d390-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="6d390-124">`https://www.example.com/foo.html`&ndash;不同子域</span><span class="sxs-lookup"><span data-stu-id="6d390-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="6d390-125">`http://example.com/foo.html`&ndash;不同方案</span><span class="sxs-lookup"><span data-stu-id="6d390-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="6d390-126">`https://example.com:9000/foo.html`&ndash;不同端口</span><span class="sxs-lookup"><span data-stu-id="6d390-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="6d390-127">比较来源时，Internet Explorer 不会考虑该端口。</span><span class="sxs-lookup"><span data-stu-id="6d390-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="6d390-128">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="6d390-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="6d390-129">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="6d390-130">以下代码通过指定源为整个应用启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="6d390-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="6d390-131">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="6d390-131">The preceding code:</span></span>

* <span data-ttu-id="6d390-132">将策略名称设置为 "\_myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="6d390-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="6d390-133">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="6d390-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="6d390-134"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>调用扩展方法，该方法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d390-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="6d390-135">使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="6d390-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="6d390-136">Lambda 采用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>对象。</span><span class="sxs-lookup"><span data-stu-id="6d390-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="6d390-137">本文稍后将介绍[配置选项](#cors-policy-options)，如`WithOrigins`。</span><span class="sxs-lookup"><span data-stu-id="6d390-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="6d390-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="6d390-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="6d390-139">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="6d390-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="6d390-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以链接方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="6d390-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="6d390-141">注意:URL**不得包含尾随**斜杠（`/`）。</span><span class="sxs-lookup"><span data-stu-id="6d390-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="6d390-142">如果 URL 以结尾`/`，则比较返回`false` ，不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6d390-143">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="6d390-143">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> <span data-ttu-id="6d390-144">通过终结点路由，CORS 中间件必须配置为在对`UseRouting`和`UseEndpoints`的调用之间执行。</span><span class="sxs-lookup"><span data-stu-id="6d390-144">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="6d390-145">配置不正确将导致中间件停止正常运行。</span><span class="sxs-lookup"><span data-stu-id="6d390-145">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="6d390-146">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="6d390-146">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="6d390-147">注意： `UseCors`必须先`UseMvc`调用。</span><span class="sxs-lookup"><span data-stu-id="6d390-147">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="6d390-148">请参阅[在 Razor Pages、控制器和操作方法中启用 cors，](#ecors)以在页面/控制器/操作级别应用 cors 策略。</span><span class="sxs-lookup"><span data-stu-id="6d390-148">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="6d390-149">有关测试上述代码的说明，请参阅[测试 CORS](#test) 。</span><span class="sxs-lookup"><span data-stu-id="6d390-149">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="6d390-150">使用终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="6d390-150">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="6d390-151">使用终结点路由，可以使用`RequireCors`一组扩展方法在每个终结点上启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d390-151">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="6d390-152">同样，也可以为所有控制器启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="6d390-152">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="6d390-153">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d390-153">Enable CORS with attributes</span></span>

<span data-ttu-id="6d390-154">EnableCors 属性提供了一种用于全局应用 CORS 的替代方法。 [ &lbrack;&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="6d390-154">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="6d390-155">`[EnableCors]`属性为所选终结点启用 CORS，而不是所有终结点。</span><span class="sxs-lookup"><span data-stu-id="6d390-155">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="6d390-156">使用`[EnableCors]`指定默认策略并`[EnableCors("{Policy String}")]`指定策略。</span><span class="sxs-lookup"><span data-stu-id="6d390-156">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="6d390-157">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="6d390-157">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="6d390-158">Razor 页面`PageModel`</span><span class="sxs-lookup"><span data-stu-id="6d390-158">Razor Page `PageModel`</span></span>
* <span data-ttu-id="6d390-159">控制器</span><span class="sxs-lookup"><span data-stu-id="6d390-159">Controller</span></span>
* <span data-ttu-id="6d390-160">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="6d390-160">Controller action method</span></span>

<span data-ttu-id="6d390-161">您可以使用`[EnableCors]`属性将不同的策略应用到控制器/页模型/操作。</span><span class="sxs-lookup"><span data-stu-id="6d390-161">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="6d390-162">如果将`[EnableCors]`属性应用于控制器/页面模型/操作方法，并在中间件中启用了 CORS，则会应用这两种策略。</span><span class="sxs-lookup"><span data-stu-id="6d390-162">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="6d390-163">建议不要结合策略。</span><span class="sxs-lookup"><span data-stu-id="6d390-163">We recommend against combining policies.</span></span> <span data-ttu-id="6d390-164">`[EnableCors]`使用特性或中间件，而不是在同一应用中。</span><span class="sxs-lookup"><span data-stu-id="6d390-164">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="6d390-165">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="6d390-165">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="6d390-166">以下代码创建 CORS 默认策略和名为`"AnotherPolicy"`的策略：</span><span class="sxs-lookup"><span data-stu-id="6d390-166">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="6d390-167">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="6d390-167">Disable CORS</span></span>

<span data-ttu-id="6d390-168">DisableCors 属性为控制器/页模型/操作禁用 CORS。 [ &lbrack;&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="6d390-168">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="6d390-169">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="6d390-169">CORS policy options</span></span>

<span data-ttu-id="6d390-170">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="6d390-170">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="6d390-171">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="6d390-171">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="6d390-172">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="6d390-172">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="6d390-173">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="6d390-173">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="6d390-174">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="6d390-174">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="6d390-175">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="6d390-175">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="6d390-176">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="6d390-176">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="6d390-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在中`Startup.ConfigureServices`调用。</span><span class="sxs-lookup"><span data-stu-id="6d390-177"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6d390-178">对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。</span><span class="sxs-lookup"><span data-stu-id="6d390-178">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="6d390-179">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="6d390-179">Set the allowed origins</span></span>

<span data-ttu-id="6d390-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>允许所有来源的 CORS 请求和任何方案（`http`或`https`）。 &ndash;</span><span class="sxs-lookup"><span data-stu-id="6d390-180"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="6d390-181">`AllowAnyOrigin`是不安全的，因为*任何网站*都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-181">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="6d390-182">指定`AllowAnyOrigin` 和`AllowCredentials`是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="6d390-182">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="6d390-183">使用这两种方法配置应用时，CORS 服务将返回无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="6d390-183">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="6d390-184">指定`AllowAnyOrigin` 和`AllowCredentials`是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="6d390-184">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="6d390-185">对于安全应用，如果客户端必须授权自身访问服务器资源，请指定准确的来源列表。</span><span class="sxs-lookup"><span data-stu-id="6d390-185">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="6d390-186">`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-186">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="6d390-187">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="6d390-187">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="6d390-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>将策略的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> &ndash;</span><span class="sxs-lookup"><span data-stu-id="6d390-188"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="6d390-189">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="6d390-189">Set the allowed HTTP methods</span></span>

<span data-ttu-id="6d390-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>：</span><span class="sxs-lookup"><span data-stu-id="6d390-190"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="6d390-191">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="6d390-191">Allows any HTTP method:</span></span>
* <span data-ttu-id="6d390-192">影响预检请求和`Access-Control-Allow-Methods`标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-192">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="6d390-193">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="6d390-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="6d390-194">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="6d390-194">Set the allowed request headers</span></span>

<span data-ttu-id="6d390-195">若要允许在 CORS 请求中发送特定标头（称为*作者请求标头*） <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ，请调用并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="6d390-195">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="6d390-196">若要允许所有作者请求标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="6d390-196">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="6d390-197">此设置会影响预检请求和`Access-Control-Request-Headers`标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-197">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="6d390-198">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="6d390-198">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6d390-199">仅当`WithHeaders` `WithHeaders`发送的标头与中所述的标头完全匹配时，才可以使用CORS中间件策略匹配指定的特定标头。`Access-Control-Request-Headers`</span><span class="sxs-lookup"><span data-stu-id="6d390-199">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="6d390-200">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="6d390-200">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="6d390-201">CORS 中间件使用以下请求标头拒绝预检请求， `Content-Language`因为（[HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)）未在`WithHeaders`中列出：</span><span class="sxs-lookup"><span data-stu-id="6d390-201">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="6d390-202">应用返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-202">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="6d390-203">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-203">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6d390-204">CORS 中间件始终允许发送四个`Access-Control-Request-Headers`标头，而不考虑在 CorsPolicy 中配置的值。</span><span class="sxs-lookup"><span data-stu-id="6d390-204">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="6d390-205">此标头列表包括：</span><span class="sxs-lookup"><span data-stu-id="6d390-205">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="6d390-206">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="6d390-206">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="6d390-207">CORS 中间件使用以下请求标头成功响应了预检请求， `Content-Language`因为始终为白名单：</span><span class="sxs-lookup"><span data-stu-id="6d390-207">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="6d390-208">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="6d390-208">Set the exposed response headers</span></span>

<span data-ttu-id="6d390-209">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-209">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="6d390-210">有关详细信息，请[参阅 W3C 跨域资源共享（术语）：简单的响应](https://www.w3.org/TR/cors/#simple-response-header)标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-210">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="6d390-211">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="6d390-211">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="6d390-212">CORS 规范将这些标头称为*简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="6d390-212">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="6d390-213">若要使其他标头可用于应用程序<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="6d390-213">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="6d390-214">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="6d390-214">Credentials in cross-origin requests</span></span>

<span data-ttu-id="6d390-215">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="6d390-215">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="6d390-216">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="6d390-216">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="6d390-217">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="6d390-217">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="6d390-218">若要使用跨域请求发送凭据，客户端必须设置`XMLHttpRequest.withCredentials`为。 `true`</span><span class="sxs-lookup"><span data-stu-id="6d390-218">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="6d390-219">直接`XMLHttpRequest`使用：</span><span class="sxs-lookup"><span data-stu-id="6d390-219">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="6d390-220">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="6d390-220">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="6d390-221">使用[提取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="6d390-221">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="6d390-222">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="6d390-222">The server must allow the credentials.</span></span> <span data-ttu-id="6d390-223">若要允许跨域凭据，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>调用：</span><span class="sxs-lookup"><span data-stu-id="6d390-223">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="6d390-224">HTTP 响应包含一个`Access-Control-Allow-Credentials`标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="6d390-224">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="6d390-225">如果浏览器发送凭据，但响应不包含有效`Access-Control-Allow-Credentials`的标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="6d390-225">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="6d390-226">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="6d390-226">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="6d390-227">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="6d390-227">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="6d390-228">CORS 规范还指出，如果标头存在`"*"` ，则将 "源" `Access-Control-Allow-Credentials`设置为 "（所有源）" 无效。</span><span class="sxs-lookup"><span data-stu-id="6d390-228">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="6d390-229">预检请求</span><span class="sxs-lookup"><span data-stu-id="6d390-229">Preflight requests</span></span>

<span data-ttu-id="6d390-230">对于某些 CORS 请求，浏览器会在发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-230">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="6d390-231">此请求称为*预检请求*。</span><span class="sxs-lookup"><span data-stu-id="6d390-231">This request is called a *preflight request*.</span></span> <span data-ttu-id="6d390-232">如果满足以下条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="6d390-232">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="6d390-233">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="6d390-233">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="6d390-234">应用不`Accept`会设置`Accept-Language` `Content-Language`、 `Last-Event-ID`、、或以外的请求标头。 `Content-Type`</span><span class="sxs-lookup"><span data-stu-id="6d390-234">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="6d390-235">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="6d390-235">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="6d390-236">为客户端请求设置的请求标头上的规则适用于应用通过`setRequestHeader` `XMLHttpRequest`在对象上调用来设置的标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-236">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="6d390-237">CORS 规范调用这些标头*作者请求标头*。</span><span class="sxs-lookup"><span data-stu-id="6d390-237">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="6d390-238">此规则不适用于浏览器可以设置的标头，如`User-Agent`、 `Host`或`Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="6d390-238">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="6d390-239">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="6d390-239">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="6d390-240">预航班请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="6d390-240">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="6d390-241">它包括两个特殊标头：</span><span class="sxs-lookup"><span data-stu-id="6d390-241">It includes two special headers:</span></span>

* <span data-ttu-id="6d390-242">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="6d390-242">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="6d390-243">`Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="6d390-243">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="6d390-244">如前文所述，这不包含浏览器设置的标头，如`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="6d390-244">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="6d390-245">CORS 预检请求可能包含一个`Access-Control-Request-Headers`标头，该标头向服务器指示与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-245">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="6d390-246">若要允许特定标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="6d390-246">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="6d390-247">若要允许所有作者请求标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="6d390-247">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="6d390-248">浏览器在设置`Access-Control-Request-Headers`方式上并不完全一致。</span><span class="sxs-lookup"><span data-stu-id="6d390-248">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="6d390-249">如果将标头`"*"`设置为（或使用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>）以外的任何内容，则应该至少`Accept`包含`Content-Type`、、 `Origin`和，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-249">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="6d390-250">下面是针对预检请求的示例响应（假定服务器允许该请求）：</span><span class="sxs-lookup"><span data-stu-id="6d390-250">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="6d390-251">响应包含一个`Access-Control-Allow-Methods`标头，其中列出了允许的方法， `Access-Control-Allow-Headers`还可以选择标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-251">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="6d390-252">如果预检请求成功，则浏览器发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-252">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="6d390-253">如果预检请求被拒绝，应用将返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-253">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="6d390-254">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-254">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="6d390-255">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="6d390-255">Set the preflight expiration time</span></span>

<span data-ttu-id="6d390-256">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="6d390-256">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="6d390-257">若要设置此标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="6d390-257">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="6d390-258">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="6d390-258">How CORS works</span></span>

<span data-ttu-id="6d390-259">本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="6d390-259">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="6d390-260">CORS**不**是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="6d390-260">CORS is **not** a security feature.</span></span> <span data-ttu-id="6d390-261">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="6d390-261">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="6d390-262">例如，恶意执行组件可能会对站点使用[阻止跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求，以窃取信息。</span><span class="sxs-lookup"><span data-stu-id="6d390-262">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="6d390-263">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="6d390-263">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="6d390-264">它由客户端（浏览器）来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d390-264">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="6d390-265">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="6d390-265">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="6d390-266">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="6d390-266">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="6d390-267">Fiddler</span><span class="sxs-lookup"><span data-stu-id="6d390-267">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="6d390-268">Postman</span><span class="sxs-lookup"><span data-stu-id="6d390-268">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="6d390-269">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="6d390-269">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="6d390-270">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="6d390-270">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="6d390-271">这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="6d390-271">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="6d390-272">浏览器（不含 CORS）不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-272">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="6d390-273">在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="6d390-273">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="6d390-274">JSONP 不使用 XHR，而是使用`<script>`标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="6d390-274">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="6d390-275">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="6d390-275">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="6d390-276">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-276">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="6d390-277">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-277">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="6d390-278">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="6d390-278">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="6d390-279">下面是一个跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="6d390-279">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="6d390-280">`Origin`标头提供发出请求的站点的域：</span><span class="sxs-lookup"><span data-stu-id="6d390-280">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="6d390-281">如果服务器允许该请求，则会在响应`Access-Control-Allow-Origin`中设置标头。</span><span class="sxs-lookup"><span data-stu-id="6d390-281">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="6d390-282">此标头的值可以与请求`Origin`中的标头相匹配，也可以`"*"`是通配符值，这意味着允许任何源：</span><span class="sxs-lookup"><span data-stu-id="6d390-282">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="6d390-283">如果响应不包含`Access-Control-Allow-Origin`标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="6d390-283">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="6d390-284">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="6d390-284">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="6d390-285">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="6d390-285">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="6d390-286">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="6d390-286">Test CORS</span></span>

<span data-ttu-id="6d390-287">测试 CORS：</span><span class="sxs-lookup"><span data-stu-id="6d390-287">To test CORS:</span></span>

1. <span data-ttu-id="6d390-288">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="6d390-288">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="6d390-289">或者，您也可以[下载该示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="6d390-289">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="6d390-290">使用本文档中的方法之一启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="6d390-290">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="6d390-291">例如:</span><span class="sxs-lookup"><span data-stu-id="6d390-291">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="6d390-292">`WithOrigins("https://localhost:<port>");`应仅用于测试示例应用程序，类似于[下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="6d390-292">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="6d390-293">创建 web 应用项目（Razor Pages 或 MVC）。</span><span class="sxs-lookup"><span data-stu-id="6d390-293">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="6d390-294">该示例使用 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="6d390-294">The sample uses Razor Pages.</span></span> <span data-ttu-id="6d390-295">可以在与 API 项目相同的解决方案中创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="6d390-295">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="6d390-296">将以下突出显示的代码添加到*索引 cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="6d390-296">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="6d390-297">在上面的代码中， `url: 'https://<web app>.azurewebsites.net/api/values/1',`将替换为已部署应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="6d390-297">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="6d390-298">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="6d390-298">Deploy the API project.</span></span> <span data-ttu-id="6d390-299">例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="6d390-299">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="6d390-300">从桌面运行 Razor Pages 或 MVC 应用，然后单击 "**测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="6d390-300">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="6d390-301">使用 F12 工具查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="6d390-301">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="6d390-302">从中`WithOrigins`删除 localhost 源并部署应用。</span><span class="sxs-lookup"><span data-stu-id="6d390-302">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="6d390-303">或者，使用其他端口运行客户端应用。</span><span class="sxs-lookup"><span data-stu-id="6d390-303">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="6d390-304">例如，在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="6d390-304">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="6d390-305">与客户端应用程序进行测试。</span><span class="sxs-lookup"><span data-stu-id="6d390-305">Test with the client app.</span></span> <span data-ttu-id="6d390-306">CORS 故障返回一个错误，但错误消息不能用于 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="6d390-306">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="6d390-307">使用 F12 工具中的 "控制台" 选项卡查看错误。</span><span class="sxs-lookup"><span data-stu-id="6d390-307">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="6d390-308">根据浏览器，你会收到类似于以下内容的错误（在 F12 工具控制台中）：</span><span class="sxs-lookup"><span data-stu-id="6d390-308">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="6d390-309">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="6d390-309">Using Microsoft Edge:</span></span>

     <span data-ttu-id="6d390-310">**SEC7120： [CORS] 源`https://localhost:44375`在跨域资源的访问控制允许源响应标头中找不到`https://localhost:44375``https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="6d390-310">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="6d390-311">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="6d390-311">Using Chrome:</span></span>

     <span data-ttu-id="6d390-312">**CORS 策略已阻止`https://webapi.azurewebsites.net/api/values/1`从原始`https://localhost:44375`位置访问 XMLHttpRequest请求的资源上不存在 "访问控制-允许-源" 标头。**</span><span class="sxs-lookup"><span data-stu-id="6d390-312">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d390-313">其他资源</span><span class="sxs-lookup"><span data-stu-id="6d390-313">Additional resources</span></span>

* [<span data-ttu-id="6d390-314">跨域资源共享（CORS）</span><span class="sxs-lookup"><span data-stu-id="6d390-314">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
