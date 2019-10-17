---
title: 在 ASP.NET Core 中启用跨域请求（CORS）
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: security/cors
ms.openlocfilehash: 3a51d365626c858ad48298a1108e37eba9050fe7
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391299"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="7f0ca-103">在 ASP.NET Core 中启用跨域请求（CORS）</span><span class="sxs-lookup"><span data-stu-id="7f0ca-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="7f0ca-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7f0ca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7f0ca-105">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="7f0ca-106">浏览器安全可以防止网页向其他域发送请求，而不是为网页提供服务。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="7f0ca-107">此限制称为*相同源策略*。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="7f0ca-108">同一源策略可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="7f0ca-109">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="7f0ca-110">有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="7f0ca-111">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="7f0ca-112">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="7f0ca-113">**不**是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="7f0ca-114">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="7f0ca-115">有关详细信息，请参阅[CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="7f0ca-116">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="7f0ca-117">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="7f0ca-118">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7f0ca-118">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="7f0ca-119">同一原点</span><span class="sxs-lookup"><span data-stu-id="7f0ca-119">Same origin</span></span>

<span data-ttu-id="7f0ca-120">如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="7f0ca-121">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="7f0ca-122">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="7f0ca-123">`https://example.net` &ndash; 个不同的域</span><span class="sxs-lookup"><span data-stu-id="7f0ca-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="7f0ca-124">`https://www.example.com/foo.html` &ndash; 个不同的子域</span><span class="sxs-lookup"><span data-stu-id="7f0ca-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="7f0ca-125">`http://example.com/foo.html` &ndash; 个不同的方案</span><span class="sxs-lookup"><span data-stu-id="7f0ca-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="7f0ca-126">`https://example.com:9000/foo.html` &ndash; 个不同端口</span><span class="sxs-lookup"><span data-stu-id="7f0ca-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="7f0ca-127">比较来源时，Internet Explorer 不会考虑该端口。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="7f0ca-128">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="7f0ca-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="7f0ca-129">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="7f0ca-130">以下代码通过指定源为整个应用启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="7f0ca-131">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-131">The preceding code:</span></span>

* <span data-ttu-id="7f0ca-132">将策略名称设置为 "\_myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="7f0ca-133">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="7f0ca-134">调用 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 扩展方法，这将启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="7f0ca-135">使用[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="7f0ca-136">Lambda 采用 @no__t 0 对象。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="7f0ca-137">本文稍后将介绍[配置选项](#cors-policy-options)，如 `WithOrigins`。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="7f0ca-138">@No__t-0 方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="7f0ca-139">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="7f0ca-140">@No__t-0 方法可以链接方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="7f0ca-141">注意： URL**不得包含尾随**斜杠（`/`）。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-141">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="7f0ca-142">如果 URL 以 `/` 终止，比较将返回 `false`，并且不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-142">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a><span data-ttu-id="7f0ca-143">将 CORS 策略应用到所有终结点</span><span class="sxs-lookup"><span data-stu-id="7f0ca-143">Apply CORS policies to all endpoints</span></span>

<span data-ttu-id="7f0ca-144">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-144">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
> <span data-ttu-id="7f0ca-145">通过终结点路由，CORS 中间件必须配置为在对 @no__t 和 `UseEndpoints` 的调用之间执行。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-145">With endpoint routing, the CORS middleware must be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="7f0ca-146">配置不正确将导致中间件停止正常运行。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-146">Incorrect configuration will cause the middleware to stop functioning correctly.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
<span data-ttu-id="7f0ca-147">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-147">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="7f0ca-148">注意： @no__t 之前，必须先调用 `UseCors`。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-148">Note: `UseCors` must be called before `UseMvc`.</span></span>

::: moniker-end

<span data-ttu-id="7f0ca-149">请参阅[在 Razor Pages、控制器和操作方法中启用 cors，](#ecors)以在页面/控制器/操作级别应用 cors 策略。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-149">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="7f0ca-150">有关测试上述代码的说明，请参阅[测试 CORS](#test) 。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-150">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="7f0ca-151">使用终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="7f0ca-151">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="7f0ca-152">使用终结点路由，可以根据每个终结点启用 CORS，使用 @no__t 的扩展方法集。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-152">With endpoint routing, CORS can be enabled on a per-endpoint basis using the `RequireCors` set of extension methods.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

<span data-ttu-id="7f0ca-153">同样，也可以为所有控制器启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-153">Similarly, CORS can also be enabled for all controllers:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="7f0ca-154">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="7f0ca-154">Enable CORS with attributes</span></span>

<span data-ttu-id="7f0ca-155">[@No__t-1EnableCors @ no__t](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-155">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="7f0ca-156">@No__t-0 特性可为选定的终结点（而不是所有终结点）启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-156">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="7f0ca-157">使用 @no__t 指定默认策略，并 `[EnableCors("{Policy String}")]` 指定策略。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-157">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="7f0ca-158">@No__t-0 特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-158">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="7f0ca-159">Razor 页 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="7f0ca-159">Razor Page `PageModel`</span></span>
* <span data-ttu-id="7f0ca-160">控制器</span><span class="sxs-lookup"><span data-stu-id="7f0ca-160">Controller</span></span>
* <span data-ttu-id="7f0ca-161">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="7f0ca-161">Controller action method</span></span>

<span data-ttu-id="7f0ca-162">您可以将不同的策略应用于控制器/页面模型/操作，`[EnableCors]` 属性。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-162">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="7f0ca-163">将 `[EnableCors]` 属性应用于控制器/页面模型/操作方法，并在中间件中启用 CORS 时，将应用这两种策略。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-163">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="7f0ca-164">建议不要结合策略。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-164">We recommend against combining policies.</span></span> <span data-ttu-id="7f0ca-165">使用同一个应用中的 `[EnableCors]` 特性或中间件。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-165">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="7f0ca-166">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-166">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="7f0ca-167">以下代码创建 CORS 默认策略和名为 `"AnotherPolicy"` 的策略：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-167">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="7f0ca-168">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="7f0ca-168">Disable CORS</span></span>

<span data-ttu-id="7f0ca-169">[@No__t-1DisableCors @ no__t-2](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性对控制器/页模型/操作禁用 CORS。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-169">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="7f0ca-170">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="7f0ca-170">CORS policy options</span></span>

<span data-ttu-id="7f0ca-171">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-171">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="7f0ca-172">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="7f0ca-172">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="7f0ca-173">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="7f0ca-173">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="7f0ca-174">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="7f0ca-174">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="7f0ca-175">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="7f0ca-175">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="7f0ca-176">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="7f0ca-176">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="7f0ca-177">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="7f0ca-177">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="7f0ca-178">`Startup.ConfigureServices` 中调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-178"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7f0ca-179">对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-179">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="7f0ca-180">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="7f0ca-180">Set the allowed origins</span></span>

<span data-ttu-id="7f0ca-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; 允许所有来源的 CORS 请求和任何方案（`http` 或 `https`）。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-181"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="7f0ca-182">`AllowAnyOrigin` 不安全，因为*任何网站*都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-182">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="7f0ca-183">指定 @no__t 0 和 @no__t 为不安全配置，可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-183">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="7f0ca-184">使用这两种方法配置应用时，CORS 服务将返回无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-184">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="7f0ca-185">指定 @no__t 0 和 @no__t 为不安全配置，可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-185">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="7f0ca-186">对于安全应用，如果客户端必须授权自身访问服务器资源，请指定准确的来源列表。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-186">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

::: moniker-end

<span data-ttu-id="7f0ca-187">`AllowAnyOrigin` 会影响预检请求和 @no__t 标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-187">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="7f0ca-188">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="7f0ca-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; 将策略的 @no__t 设置为在评估是否允许源时允许源与配置的通配符域匹配的函数。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-189"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="7f0ca-190">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="7f0ca-190">Set the allowed HTTP methods</span></span>

<span data-ttu-id="7f0ca-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-191"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="7f0ca-192">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-192">Allows any HTTP method:</span></span>
* <span data-ttu-id="7f0ca-193">影响预检请求和 @no__t 0 标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-193">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="7f0ca-194">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-194">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="7f0ca-195">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="7f0ca-195">Set the allowed request headers</span></span>

<span data-ttu-id="7f0ca-196">若要允许在 CORS 请求中发送特定标头（称为*作者请求标头*），请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-196">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="7f0ca-197">若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-197">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="7f0ca-198">此设置会影响预检请求和 @no__t 0 标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-198">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="7f0ca-199">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-199">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7f0ca-200">仅当在 `Access-Control-Request-Headers` 中发送的标头与 `WithHeaders` 中指定的标头完全匹配时，才可以使用 CORS 中间件策略与 `WithHeaders` 指定的特定标头匹配。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-200">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="7f0ca-201">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="7f0ca-202">CORS 中间件使用以下请求标头拒绝预检请求，因为 `WithHeaders` 中未列出 `Content-Language` （[HeaderNames](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)）：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-202">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="7f0ca-203">应用返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-203">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="7f0ca-204">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-204">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7f0ca-205">CORS 中间件始终允许发送 `Access-Control-Request-Headers` 中的四个标头，而不考虑在 CorsPolicy 中配置的值。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-205">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="7f0ca-206">此标头列表包括：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-206">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="7f0ca-207">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-207">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="7f0ca-208">CORS 中间件使用以下请求标头成功响应预检请求，因为 `Content-Language` 始终为白名单：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-208">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="7f0ca-209">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="7f0ca-209">Set the exposed response headers</span></span>

<span data-ttu-id="7f0ca-210">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-210">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="7f0ca-211">有关详细信息，请参阅[W3C 跨域资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-211">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="7f0ca-212">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-212">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="7f0ca-213">CORS 规范将这些标头称为*简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-213">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="7f0ca-214">若要使其他标头可用于应用程序，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-214">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="7f0ca-215">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="7f0ca-215">Credentials in cross-origin requests</span></span>

<span data-ttu-id="7f0ca-216">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-216">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="7f0ca-217">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-217">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="7f0ca-218">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-218">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="7f0ca-219">若要使用跨域请求发送凭据，客户端必须将 `XMLHttpRequest.withCredentials` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-219">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="7f0ca-220">直接使用 @no__t：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-220">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="7f0ca-221">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-221">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="7f0ca-222">使用[提取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-222">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="7f0ca-223">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-223">The server must allow the credentials.</span></span> <span data-ttu-id="7f0ca-224">若要允许跨域凭据，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-224">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="7f0ca-225">HTTP 响应包含一个 @no__t 0 的标头，该标头通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-225">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="7f0ca-226">如果浏览器发送凭据，但响应不包含有效的 @no__t 0 标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-226">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="7f0ca-227">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-227">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="7f0ca-228">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-228">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="7f0ca-229">CORS 规范还指出，如果 @no__t 标头存在，则将源设置为 `"*"` （所有源）是无效的。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-229">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="7f0ca-230">预检请求</span><span class="sxs-lookup"><span data-stu-id="7f0ca-230">Preflight requests</span></span>

<span data-ttu-id="7f0ca-231">对于某些 CORS 请求，浏览器会在发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-231">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="7f0ca-232">此请求称为*预检请求*。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-232">This request is called a *preflight request*.</span></span> <span data-ttu-id="7f0ca-233">如果满足以下条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-233">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="7f0ca-234">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-234">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="7f0ca-235">应用未设置 `Accept`、`Accept-Language`、`Content-Language`、`Content-Type` 或 @no__t 为的请求标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-235">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="7f0ca-236">@No__t 的标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-236">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="7f0ca-237">为客户端请求设置的请求标头上的规则适用于应用通过调用 @no__t @no__t 对象上的的标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-237">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="7f0ca-238">CORS 规范调用这些标头*作者请求标头*。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-238">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="7f0ca-239">规则不适用于浏览器可以设置的标头，如 `User-Agent`、`Host` 或 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-239">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="7f0ca-240">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-240">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="7f0ca-241">预航班请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-241">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="7f0ca-242">它包括两个特殊标头：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-242">It includes two special headers:</span></span>

* <span data-ttu-id="7f0ca-243">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-243">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="7f0ca-244">`Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-244">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="7f0ca-245">如前文所述，这不包含浏览器设置的标头，如 `User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-245">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="7f0ca-246">CORS 预检请求可能包括一个 @no__t 0 标头，该标头向服务器指示与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-246">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="7f0ca-247">若要允许特定标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-247">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="7f0ca-248">若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-248">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="7f0ca-249">浏览器的设置方式并不完全一致 `Access-Control-Request-Headers`。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-249">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="7f0ca-250">如果将标头设置为 @no__t 0 （或使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>）以外的任何内容，则至少应包含 `Accept`、`Content-Type` 和 @no__t，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-250">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="7f0ca-251">下面是针对预检请求的示例响应（假定服务器允许该请求）：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-251">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="7f0ca-252">响应包括一个 @no__t 0 标头，该标头列出了允许的方法，还可以选择一个 @no__t 标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-252">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="7f0ca-253">如果预检请求成功，则浏览器发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-253">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="7f0ca-254">如果预检请求被拒绝，应用将返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-254">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="7f0ca-255">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-255">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="7f0ca-256">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="7f0ca-256">Set the preflight expiration time</span></span>

<span data-ttu-id="7f0ca-257">@No__t 的标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-257">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="7f0ca-258">若要设置此标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-258">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="7f0ca-259">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="7f0ca-259">How CORS works</span></span>

<span data-ttu-id="7f0ca-260">本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-260">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="7f0ca-261">CORS**不**是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-261">CORS is **not** a security feature.</span></span> <span data-ttu-id="7f0ca-262">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-262">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="7f0ca-263">例如，恶意执行组件可能会对站点使用[阻止跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求，以窃取信息。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-263">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="7f0ca-264">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-264">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="7f0ca-265">它由客户端（浏览器）来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-265">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="7f0ca-266">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-266">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="7f0ca-267">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-267">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="7f0ca-268">Fiddler</span><span class="sxs-lookup"><span data-stu-id="7f0ca-268">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="7f0ca-269">Postman</span><span class="sxs-lookup"><span data-stu-id="7f0ca-269">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="7f0ca-270">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="7f0ca-270">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="7f0ca-271">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-271">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="7f0ca-272">这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-272">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="7f0ca-273">浏览器（不含 CORS）不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-273">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="7f0ca-274">在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-274">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="7f0ca-275">JSONP 不使用 XHR，它使用 `<script>` 标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-275">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="7f0ca-276">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-276">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="7f0ca-277">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-277">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="7f0ca-278">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-278">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="7f0ca-279">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-279">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="7f0ca-280">下面是一个跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-280">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="7f0ca-281">@No__t 0 标头提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-281">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="7f0ca-282">@No__t 的标头是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-282">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="7f0ca-283">如果服务器允许该请求，则会在响应中设置 @no__t 的标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-283">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="7f0ca-284">此标头的值可以与请求中的 @no__t 0 标头匹配，也可以是通配符值 `"*"`，表示允许任何源：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-284">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="7f0ca-285">如果响应不包括 @no__t 的标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-285">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="7f0ca-286">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-286">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="7f0ca-287">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-287">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="7f0ca-288">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="7f0ca-288">Test CORS</span></span>

<span data-ttu-id="7f0ca-289">测试 CORS：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-289">To test CORS:</span></span>

1. <span data-ttu-id="7f0ca-290">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-290">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="7f0ca-291">或者，您也可以[下载该示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-291">Alternatively, you can [download the sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="7f0ca-292">使用本文档中的方法之一启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-292">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="7f0ca-293">例如:</span><span class="sxs-lookup"><span data-stu-id="7f0ca-293">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="7f0ca-294">`WithOrigins("https://localhost:<port>");` 应仅用于测试类似于[下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)的示例应用。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-294">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="7f0ca-295">创建 web 应用项目（Razor Pages 或 MVC）。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-295">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="7f0ca-296">该示例使用 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-296">The sample uses Razor Pages.</span></span> <span data-ttu-id="7f0ca-297">可以在与 API 项目相同的解决方案中创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-297">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="7f0ca-298">将以下突出显示的代码添加到*索引 cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-298">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="7f0ca-299">在上面的代码中，将 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 替换为已部署应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-299">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="7f0ca-300">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-300">Deploy the API project.</span></span> <span data-ttu-id="7f0ca-301">例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-301">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="7f0ca-302">从桌面运行 Razor Pages 或 MVC 应用，然后单击 "**测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-302">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="7f0ca-303">使用 F12 工具查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-303">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="7f0ca-304">从 @no__t 中删除 localhost 源，并部署应用。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-304">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="7f0ca-305">或者，使用其他端口运行客户端应用。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-305">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="7f0ca-306">例如，在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-306">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="7f0ca-307">与客户端应用程序进行测试。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-307">Test with the client app.</span></span> <span data-ttu-id="7f0ca-308">CORS 故障返回一个错误，但错误消息不能用于 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-308">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="7f0ca-309">使用 F12 工具中的 "控制台" 选项卡查看错误。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-309">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="7f0ca-310">根据浏览器，你会收到类似于以下内容的错误（在 F12 工具控制台中）：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-310">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="7f0ca-311">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-311">Using Microsoft Edge:</span></span>

     <span data-ttu-id="7f0ca-312">**SEC7120： [CORS] 源 `https://localhost:44375` 在 @no__t 上找不到跨源资源的访问控制允许源响应标头中的 `https://localhost:44375`**</span><span class="sxs-lookup"><span data-stu-id="7f0ca-312">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="7f0ca-313">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-313">Using Chrome:</span></span>

     <span data-ttu-id="7f0ca-314">**对源 `https://localhost:44375` `https://webapi.azurewebsites.net/api/values/1` 的 XMLHttpRequest 的访问已被 CORS 策略阻止：请求的资源上没有 "访问控制-允许" 标头。**</span><span class="sxs-lookup"><span data-stu-id="7f0ca-314">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="7f0ca-315">可以使用工具（如[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)）测试启用 CORS 的终结点。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-315">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="7f0ca-316">使用工具时，@no__t 的标头指定的请求源必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-316">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="7f0ca-317">如果请求不是基于 `Origin` 标头的值*跨*域的，则：</span><span class="sxs-lookup"><span data-stu-id="7f0ca-317">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="7f0ca-318">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-318">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="7f0ca-319">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="7f0ca-319">CORS headers aren't returned in the response.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7f0ca-320">其他资源</span><span class="sxs-lookup"><span data-stu-id="7f0ca-320">Additional resources</span></span>

* [<span data-ttu-id="7f0ca-321">跨域资源共享（CORS）</span><span class="sxs-lookup"><span data-stu-id="7f0ca-321">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
