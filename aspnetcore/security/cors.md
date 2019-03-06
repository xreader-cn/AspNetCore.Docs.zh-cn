---
title: 启用 ASP.NET Core 中的跨域请求 (CORS)
author: rick-anderson
description: 了解如何作为一种标准允许或拒绝在 ASP.NET Core 应用中的跨域请求的 CORS。
ms.author: riande
ms.custom: mvc
ms.date: 02/27/2019
uid: security/cors
ms.openlocfilehash: eb8dd3b1c96d9060b0164dcd4d0fbe004ed4af84
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346367"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="e0222-103">启用 ASP.NET Core 中的跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="e0222-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

<span data-ttu-id="e0222-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e0222-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e0222-105">本文介绍如何在 ASP.NET Core 应用中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="e0222-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="e0222-106">浏览器安全性以防止网页从发出请求到不同的域的 web 页面提供服务。</span><span class="sxs-lookup"><span data-stu-id="e0222-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="e0222-107">此限制称为*同域策略*。</span><span class="sxs-lookup"><span data-stu-id="e0222-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="e0222-108">同域策略可阻止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="e0222-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="e0222-109">有时，你可能想要允许其他站点对您的应用程序进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-109">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="e0222-110">有关详细信息，请参阅[Mozilla CORS 文章](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="e0222-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="e0222-111">[跨源资源共享](https://www.w3.org/TR/cors/)(CORS):</span><span class="sxs-lookup"><span data-stu-id="e0222-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="e0222-112">是一项 W3C 标准，让服务器放宽同域策略。</span><span class="sxs-lookup"><span data-stu-id="e0222-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="e0222-113">是**不**一项安全功能，CORS 放松了安全性。</span><span class="sxs-lookup"><span data-stu-id="e0222-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="e0222-114">通过允许 CORS API 不更安全。</span><span class="sxs-lookup"><span data-stu-id="e0222-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="e0222-115">有关详细信息，请参阅[如何 CORS 工作](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="e0222-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="e0222-116">允许显式允许某些跨域请求，同时拒绝另一个服务器。</span><span class="sxs-lookup"><span data-stu-id="e0222-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="e0222-117">如是更安全、 更灵活早期技术相比[JSONP](/dotnet/framework/wcf/samples/jsonp)。</span><span class="sxs-lookup"><span data-stu-id="e0222-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="e0222-118">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e0222-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="e0222-119">相同的原点</span><span class="sxs-lookup"><span data-stu-id="e0222-119">Same origin</span></span>

<span data-ttu-id="e0222-120">如果它们具有相同方案、 主机和端口，两个 Url 将具有相同的原点 ([RFC 6454](https://tools.ietf.org/html/rfc6454))。</span><span class="sxs-lookup"><span data-stu-id="e0222-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="e0222-121">以下两个 Url 具有相同的原点：</span><span class="sxs-lookup"><span data-stu-id="e0222-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="e0222-122">这些 Url 具有不同的源，比以前的两个 Url:</span><span class="sxs-lookup"><span data-stu-id="e0222-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="e0222-123">`https://example.net` &ndash; 不同的域</span><span class="sxs-lookup"><span data-stu-id="e0222-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="e0222-124">`https://www.example.com/foo.html` &ndash; 不同的子域</span><span class="sxs-lookup"><span data-stu-id="e0222-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="e0222-125">`http://example.com/foo.html` &ndash; 不同的方案</span><span class="sxs-lookup"><span data-stu-id="e0222-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="e0222-126">`https://example.com:9000/foo.html` &ndash; 不同的端口</span><span class="sxs-lookup"><span data-stu-id="e0222-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="e0222-127">比较来源时，Internet Explorer 不会考虑该端口。</span><span class="sxs-lookup"><span data-stu-id="e0222-127">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="e0222-128">使用命名的策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="e0222-128">CORS with named policy and middleware</span></span>

<span data-ttu-id="e0222-129">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-129">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="e0222-130">下面的代码指定原点整个应用启用 CORS:</span><span class="sxs-lookup"><span data-stu-id="e0222-130">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="e0222-131">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="e0222-131">The preceding code:</span></span>

* <span data-ttu-id="e0222-132">策略名称设置为"\_myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="e0222-132">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="e0222-133">策略名称是任意的。</span><span class="sxs-lookup"><span data-stu-id="e0222-133">The policy name is arbitrary.</span></span>
* <span data-ttu-id="e0222-134">调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法，使内核。</span><span class="sxs-lookup"><span data-stu-id="e0222-134">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables cores.</span></span>
* <span data-ttu-id="e0222-135">调用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>与[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)。</span><span class="sxs-lookup"><span data-stu-id="e0222-135">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="e0222-136">Lambda 采用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>对象。</span><span class="sxs-lookup"><span data-stu-id="e0222-136">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="e0222-137">[配置选项](#cors-policy-options)，如`WithOrigins`，本文稍后介绍。</span><span class="sxs-lookup"><span data-stu-id="e0222-137">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="e0222-138"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用程序的服务容器：</span><span class="sxs-lookup"><span data-stu-id="e0222-138">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="e0222-139">有关详细信息，请参阅[CORS 策略选项](#cpo)本文档中。</span><span class="sxs-lookup"><span data-stu-id="e0222-139">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="e0222-140"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以将链接的方法，如下面的代码中所示：</span><span class="sxs-lookup"><span data-stu-id="e0222-140">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="e0222-141">以下突出显示的代码适用于所有应用程序终结点通过 CORS 中间件的 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="e0222-141">The following highlighted code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>

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

<span data-ttu-id="e0222-142">请参阅[Razor 页面、 控制器和操作方法中启用 CORS](#ecors)要应用页/控制器/操作级的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="e0222-142">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="e0222-143">注意:</span><span class="sxs-lookup"><span data-stu-id="e0222-143">Note:</span></span>

* <span data-ttu-id="e0222-144">`UseCors` 必须在`UseMvc` 之前调用。</span><span class="sxs-lookup"><span data-stu-id="e0222-144">`UseCors` must be called before `UseMvc`.</span></span>
* <span data-ttu-id="e0222-145">URL 必须**不**包含尾部反斜杠 (`/`)。</span><span class="sxs-lookup"><span data-stu-id="e0222-145">The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="e0222-146">如果 URL 以终止`/`，则比较操作返回`false`并返回无标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-146">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="e0222-147">请参阅[测试 CORS](#test)有关测试前面的代码中的说明。</span><span class="sxs-lookup"><span data-stu-id="e0222-147">See [Test CORS](#test) for instructions on testing the preceding code.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="e0222-148">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="e0222-148">Enable CORS with attributes</span></span>

<span data-ttu-id="e0222-149">[ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="e0222-149">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="e0222-150">`[EnableCors]`属性为所选的终结点，而不是所有终结点启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="e0222-150">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="e0222-151">使用`[EnableCors]`可以指定的默认策略和`[EnableCors("{Policy String}")]`指定策略。</span><span class="sxs-lookup"><span data-stu-id="e0222-151">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="e0222-152">`[EnableCors]`特性可以应用于：</span><span class="sxs-lookup"><span data-stu-id="e0222-152">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="e0222-153">Razor 页面 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="e0222-153">Razor Page `PageModel`</span></span>
* <span data-ttu-id="e0222-154">控制器</span><span class="sxs-lookup"><span data-stu-id="e0222-154">Controller</span></span>
* <span data-ttu-id="e0222-155">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="e0222-155">Controller action method</span></span>

<span data-ttu-id="e0222-156">可以将不同的策略应用于控制器/页的模型/操作与`[EnableCors]`属性。</span><span class="sxs-lookup"><span data-stu-id="e0222-156">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="e0222-157">当`[EnableCors]`特性应用到控制器/页的模型/操作方法和中间件中启用了 CORS，这两个策略的应用。</span><span class="sxs-lookup"><span data-stu-id="e0222-157">When the `[EnableCors]` attribute is applied to a controllers/page-model/action method, and CORS is enabled in middleware, both policies are applied.</span></span> <span data-ttu-id="e0222-158">我们建议不要合并策略。</span><span class="sxs-lookup"><span data-stu-id="e0222-158">We recommend against combining policies.</span></span> <span data-ttu-id="e0222-159">使用`[EnableCors]`属性或中间件，不能同时在同一应用中。</span><span class="sxs-lookup"><span data-stu-id="e0222-159">Use the `[EnableCors]` attribute or middleware, not both in the same app.</span></span>

<span data-ttu-id="e0222-160">下面的代码将不同的策略应用到每个方法：</span><span class="sxs-lookup"><span data-stu-id="e0222-160">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="e0222-161">以下代码将创建一个 CORS 默认策略和一个名为策略`"AnotherPolicy"`:</span><span class="sxs-lookup"><span data-stu-id="e0222-161">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="e0222-162">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="e0222-162">Disable CORS</span></span>

<span data-ttu-id="e0222-163">[ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性控制器/页的模型/操作禁用 CORS。</span><span class="sxs-lookup"><span data-stu-id="e0222-163">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="e0222-164">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="e0222-164">CORS policy options</span></span>

<span data-ttu-id="e0222-165">本部分介绍可以在 CORS 策略设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="e0222-165">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="e0222-166">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="e0222-166">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="e0222-167">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="e0222-167">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="e0222-168">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="e0222-168">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="e0222-169">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="e0222-169">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="e0222-170">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="e0222-170">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="e0222-171">将预检过期时间设置</span><span class="sxs-lookup"><span data-stu-id="e0222-171">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

 <span data-ttu-id="e0222-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中称为`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="e0222-172"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e0222-173">有关一些选项，可能会有帮助读取[如何 CORS 工作](#how-cors)部分第一次。</span><span class="sxs-lookup"><span data-stu-id="e0222-173">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="e0222-174">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="e0222-174">Set the allowed origins</span></span>

<span data-ttu-id="e0222-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; 允许来自任何方案使用所有来源的 CORS 请求 (`http`或`https`)。</span><span class="sxs-lookup"><span data-stu-id="e0222-175"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="e0222-176">`AllowAnyOrigin` 不安全，因为*的任何网站*可以对应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-176">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

  ::: moniker range=">= aspnetcore-2.2"

  > [!NOTE]
  > <span data-ttu-id="e0222-177">指定`AllowAnyOrigin`和`AllowCredentials`是不安全的配置，可能会导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="e0222-177">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e0222-178">使用这两种方法配置应用程序时，CORS 服务返回了无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="e0222-178">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

  ::: moniker-end

  ::: moniker range="< aspnetcore-2.2"

  > [!NOTE]
  > <span data-ttu-id="e0222-179">指定`AllowAnyOrigin`和`AllowCredentials`是不安全的配置，可能会导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="e0222-179">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="e0222-180">有关安全的应用程序，如果客户端必须先授权本身访问服务器资源指定确切的来源列表。</span><span class="sxs-lookup"><span data-stu-id="e0222-180">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

  ::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**. 
-->

  <span data-ttu-id="e0222-181">`AllowAnyOrigin` 影响预检请求和`Access-Control-Allow-Origin`标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-181">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="e0222-182">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="e0222-182">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e0222-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; 集<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>策略允许来源以匹配配置的通配符域，如果允许原点在评估时的函数的属性。</span><span class="sxs-lookup"><span data-stu-id="e0222-183"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

  [!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="e0222-184">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="e0222-184">Set the allowed HTTP methods</span></span>

<span data-ttu-id="e0222-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>：</span><span class="sxs-lookup"><span data-stu-id="e0222-185"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="e0222-186">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="e0222-186">Allows any HTTP method:</span></span>
* <span data-ttu-id="e0222-187">影响预检请求和`Access-Control-Allow-Methods`标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-187">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="e0222-188">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="e0222-188">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="e0222-189">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="e0222-189">Set the allowed request headers</span></span>

<span data-ttu-id="e0222-190">若要允许特定标头发送在 CORS 请求中，调用*创作请求标头*，调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="e0222-190">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e0222-191">若要允许所有作者的请求标头调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="e0222-191">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e0222-192">此设置会影响的预检请求和`Access-Control-Request-Headers`标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-192">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="e0222-193">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="e0222-193">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e0222-194">CORS 中间件策略匹配所指定的特定标头`WithHeaders`时，才可以发送标头`Access-Control-Request-Headers`与中所述的标头完全匹配`WithHeaders`。</span><span class="sxs-lookup"><span data-stu-id="e0222-194">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="e0222-195">例如，假设配置，如下所示的应用：</span><span class="sxs-lookup"><span data-stu-id="e0222-195">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e0222-196">CORS 中间件拒绝包含以下请求标头的预检请求，因为`Content-Language`([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) 中未列出`WithHeaders`:</span><span class="sxs-lookup"><span data-stu-id="e0222-196">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="e0222-197">应用程序返回*200 确定*响应但不会发送回的 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-197">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e0222-198">因此，在浏览器不会尝试执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-198">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e0222-199">CORS 中间件始终允许在四个标头`Access-Control-Request-Headers`发送而不考虑 CorsPolicy.Headers 中配置的值。</span><span class="sxs-lookup"><span data-stu-id="e0222-199">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="e0222-200">此列表的标头包括：</span><span class="sxs-lookup"><span data-stu-id="e0222-200">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="e0222-201">例如，假设配置，如下所示的应用：</span><span class="sxs-lookup"><span data-stu-id="e0222-201">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="e0222-202">CORS 中间件成功响应以下请求标头的预检请求因为`Content-Language`始终是已列入允许列表：</span><span class="sxs-lookup"><span data-stu-id="e0222-202">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="e0222-203">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="e0222-203">Set the exposed response headers</span></span>

<span data-ttu-id="e0222-204">默认情况下，在浏览器不会公开所有向应用程序的响应标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-204">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="e0222-205">有关详细信息，请参阅[W3C 跨域资源共享 （术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="e0222-205">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="e0222-206">默认情况下可用的响应标头是：</span><span class="sxs-lookup"><span data-stu-id="e0222-206">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="e0222-207">CORS 规范调用这些标头*简单响应标头*。</span><span class="sxs-lookup"><span data-stu-id="e0222-207">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="e0222-208">若要使其他标头可用于应用程序，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="e0222-208">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="e0222-209">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="e0222-209">Credentials in cross-origin requests</span></span>

<span data-ttu-id="e0222-210">凭据要求特殊处理 CORS 请求中。</span><span class="sxs-lookup"><span data-stu-id="e0222-210">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="e0222-211">默认情况下，在浏览器不会发送与跨域请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="e0222-211">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="e0222-212">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="e0222-212">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="e0222-213">若要将发送具有跨源请求的凭据，客户端必须设置`XMLHttpRequest.withCredentials`到`true`。</span><span class="sxs-lookup"><span data-stu-id="e0222-213">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="e0222-214">使用`XMLHttpRequest`直接：</span><span class="sxs-lookup"><span data-stu-id="e0222-214">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="e0222-215">使用 jQuery:</span><span class="sxs-lookup"><span data-stu-id="e0222-215">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="e0222-216">使用[提取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="e0222-216">Using the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="e0222-217">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="e0222-217">The server must allow the credentials.</span></span> <span data-ttu-id="e0222-218">若要允许跨域凭据，调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span><span class="sxs-lookup"><span data-stu-id="e0222-218">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="e0222-219">HTTP 响应包括`Access-Control-Allow-Credentials`标头，服务器允许跨域请求凭据将告诉浏览器。</span><span class="sxs-lookup"><span data-stu-id="e0222-219">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="e0222-220">如果浏览器发送凭据，但响应不包含有效`Access-Control-Allow-Credentials`标头，在浏览器不会公开的响应应用程序中，并且跨域请求失败。</span><span class="sxs-lookup"><span data-stu-id="e0222-220">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="e0222-221">允许跨域凭据会安全风险。</span><span class="sxs-lookup"><span data-stu-id="e0222-221">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="e0222-222">在另一个域的网站可以向应用而无需在用户不知情的用户的名义发送登录的用户的凭据。</span><span class="sxs-lookup"><span data-stu-id="e0222-222">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="e0222-223">CORS 规范还会说明该设置到来源`"*"`（所有来源） 是无效的如果`Access-Control-Allow-Credentials`标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-223">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="e0222-224">预检请求</span><span class="sxs-lookup"><span data-stu-id="e0222-224">Preflight requests</span></span>

<span data-ttu-id="e0222-225">对于某些 CORS 请求，在浏览器发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-225">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="e0222-226">调用此请求*预检请求*。</span><span class="sxs-lookup"><span data-stu-id="e0222-226">This request is called a *preflight request*.</span></span> <span data-ttu-id="e0222-227">在浏览器可以跳过预检请求，如果满足以下条件：</span><span class="sxs-lookup"><span data-stu-id="e0222-227">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="e0222-228">请求方法是 GET、 HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="e0222-228">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="e0222-229">应用程序不会设置以外的其他请求标头`Accept`， `Accept-Language`， `Content-Language`， `Content-Type`，或`Last-Event-ID`。</span><span class="sxs-lookup"><span data-stu-id="e0222-229">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="e0222-230">`Content-Type`标头，如果设置，具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="e0222-230">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="e0222-231">在请求标头的规则集的客户端请求适用于通过调用来设置应用程序的标头`setRequestHeader`上`XMLHttpRequest`对象。</span><span class="sxs-lookup"><span data-stu-id="e0222-231">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="e0222-232">CORS 规范调用这些标头*创作请求标头*。</span><span class="sxs-lookup"><span data-stu-id="e0222-232">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="e0222-233">该规则不能应用于标头设置可以在浏览器，如`User-Agent`， `Host`，或`Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="e0222-233">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="e0222-234">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="e0222-234">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="e0222-235">预检请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="e0222-235">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="e0222-236">它包括两个特殊的标头：</span><span class="sxs-lookup"><span data-stu-id="e0222-236">It includes two special headers:</span></span>

* <span data-ttu-id="e0222-237">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="e0222-237">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="e0222-238">`Access-Control-Request-Headers`：应用程序设置实际请求的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="e0222-238">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="e0222-239">如前面所述，这不包括标头的浏览器设置，如`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="e0222-239">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<span data-ttu-id="e0222-240">CORS 预检请求可能包括`Access-Control-Request-Headers`标头，指示服务器与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-240">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="e0222-241">若要允许的特定标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span><span class="sxs-lookup"><span data-stu-id="e0222-241">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="e0222-242">若要允许所有作者的请求标头调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span><span class="sxs-lookup"><span data-stu-id="e0222-242">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="e0222-243">浏览器不是在设置方式完全一致`Access-Control-Request-Headers`。</span><span class="sxs-lookup"><span data-stu-id="e0222-243">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="e0222-244">如果将标头设置到的任何内容以外`"*"`(或使用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>)，至少应包含`Accept`， `Content-Type`，和`Origin`，以及你想要支持任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-244">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="e0222-245">下面是预检请求 （假设服务器允许请求） 的示例响应：</span><span class="sxs-lookup"><span data-stu-id="e0222-245">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="e0222-246">响应包括`Access-Control-Allow-Methods`标头，其中列出了允许的方法和 （可选）`Access-Control-Allow-Headers`标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-246">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="e0222-247">如果预检请求成功，则浏览器发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-247">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="e0222-248">如果预检请求被拒绝，该应用程序将返回*200 确定*响应但不会发送回的 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-248">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="e0222-249">因此，在浏览器不会尝试执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-249">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="e0222-250">将预检过期时间设置</span><span class="sxs-lookup"><span data-stu-id="e0222-250">Set the preflight expiration time</span></span>

<span data-ttu-id="e0222-251">`Access-Control-Max-Age`标头指定可以缓存预检请求的响应的时间。</span><span class="sxs-lookup"><span data-stu-id="e0222-251">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="e0222-252">若要设置此标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span><span class="sxs-lookup"><span data-stu-id="e0222-252">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="e0222-253">CORS 的工作原理</span><span class="sxs-lookup"><span data-stu-id="e0222-253">How CORS works</span></span>

<span data-ttu-id="e0222-254">本部分介绍了中会发生什么情况[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)请求在 HTTP 消息的级别。</span><span class="sxs-lookup"><span data-stu-id="e0222-254">This section describes what happens in a [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="e0222-255">CORS 是**不**一项安全功能。</span><span class="sxs-lookup"><span data-stu-id="e0222-255">CORS is **not** a security feature.</span></span> <span data-ttu-id="e0222-256">CORS 是一项 W3C 标准，可让服务器放宽同域策略。</span><span class="sxs-lookup"><span data-stu-id="e0222-256">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="e0222-257">例如，可以使用恶意行动者[防止跨站点脚本 (XSS)](xref:security/cross-site-scripting)针对你的站点并执行对其启用 CORS 的站点的跨站点请求窃取信息。</span><span class="sxs-lookup"><span data-stu-id="e0222-257">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="e0222-258">你的 API 不是更安全，从而 CORS。</span><span class="sxs-lookup"><span data-stu-id="e0222-258">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="e0222-259">它由客户端 （浏览器） 强制实施 CORS。</span><span class="sxs-lookup"><span data-stu-id="e0222-259">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="e0222-260">服务器执行该请求，并返回响应，而是返回错误和块的响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="e0222-260">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="e0222-261">例如，以下任何工具将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="e0222-261">For example, any of the following tools will display the server response:</span></span>
     * [<span data-ttu-id="e0222-262">Fiddler</span><span class="sxs-lookup"><span data-stu-id="e0222-262">Fiddler</span></span>](https://www.telerik.com/fiddler)
     * [<span data-ttu-id="e0222-263">Postman</span><span class="sxs-lookup"><span data-stu-id="e0222-263">Postman</span></span>](https://www.getpostman.com/)
     * [<span data-ttu-id="e0222-264">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="e0222-264">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
     * <span data-ttu-id="e0222-265">通过在地址栏中输入 URL web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="e0222-265">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="e0222-266">它是服务器以允许浏览器执行跨域的方法[XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)或[提取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)否则会被禁止的请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-266">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="e0222-267">（不 CORS) 的浏览器不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-267">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="e0222-268">CORS 前, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)用于绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="e0222-268">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="e0222-269">JSONP 不会使用 XHR，它使用`<script>`标记接收的响应。</span><span class="sxs-lookup"><span data-stu-id="e0222-269">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="e0222-270">允许脚本要加载的跨域。</span><span class="sxs-lookup"><span data-stu-id="e0222-270">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="e0222-271">[CORS 规范]()引入了几个新的 HTTP 标头启用跨域请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-271">The [CORS specification]() introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="e0222-272">如果浏览器支持 CORS，则将设置自动跨域请求这些标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-272">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="e0222-273">自定义 JavaScript 代码不需要启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="e0222-273">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="e0222-274">下面是跨域请求的示例。</span><span class="sxs-lookup"><span data-stu-id="e0222-274">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="e0222-275">`Origin`标头提供发出请求的站点的域：</span><span class="sxs-lookup"><span data-stu-id="e0222-275">The `Origin` header provides the domain of the site that's making the request:</span></span>

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

<span data-ttu-id="e0222-276">如果服务器允许该请求，它会设置`Access-Control-Allow-Origin`在响应中的标头。</span><span class="sxs-lookup"><span data-stu-id="e0222-276">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="e0222-277">此标头的值或者与匹配`Origin`请求标头或为通配符值`"*"`，允许任何源的含义：</span><span class="sxs-lookup"><span data-stu-id="e0222-277">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="e0222-278">如果响应不包括`Access-Control-Allow-Origin`标头，跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="e0222-278">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="e0222-279">具体而言，在浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="e0222-279">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="e0222-280">即使服务器返回成功的响应，请在浏览器不使响应可供客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="e0222-280">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="e0222-281">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="e0222-281">Test CORS</span></span>

<span data-ttu-id="e0222-282">若要测试 CORS:</span><span class="sxs-lookup"><span data-stu-id="e0222-282">To test CORS:</span></span>

1. <span data-ttu-id="e0222-283">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="e0222-283">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="e0222-284">此外，也可以[下载示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="e0222-284">Alternatively, you can [download the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="e0222-285">启用 CORS 的方法之一使用本文档中。</span><span class="sxs-lookup"><span data-stu-id="e0222-285">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="e0222-286">例如：</span><span class="sxs-lookup"><span data-stu-id="e0222-286">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]
  
  > [!WARNING]
  > <span data-ttu-id="e0222-287">`WithOrigins("https://localhost:<port>");` 应仅用于测试示例应用类似于[下载示例代码](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="e0222-287">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="e0222-288">创建 web 应用程序项目 （Razor 页面或 MVC）。</span><span class="sxs-lookup"><span data-stu-id="e0222-288">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="e0222-289">此示例使用 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="e0222-289">The sample uses Razor Pages.</span></span> <span data-ttu-id="e0222-290">可以为 API 项目在同一解决方案中创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="e0222-290">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="e0222-291">将以下突出显示的代码添加到*Index.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="e0222-291">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="e0222-292">在上述代码中，将为`url: 'https://<web app>.azurewebsites.net/api/values/1',`已部署的应用的 url。</span><span class="sxs-lookup"><span data-stu-id="e0222-292">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="e0222-293">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="e0222-293">Deploy the API project.</span></span> <span data-ttu-id="e0222-294">例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="e0222-294">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="e0222-295">从桌面上运行的 Razor 页面或 MVC 应用并单击**测试**按钮。</span><span class="sxs-lookup"><span data-stu-id="e0222-295">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="e0222-296">使用 F12 工具来查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="e0222-296">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="e0222-297">删除从 localhost 原点`WithOrigins`并将其部署应用程序。</span><span class="sxs-lookup"><span data-stu-id="e0222-297">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="e0222-298">此外，运行客户端应用程序，使用不同的端口。</span><span class="sxs-lookup"><span data-stu-id="e0222-298">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="e0222-299">例如，从 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="e0222-299">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="e0222-300">使用客户端应用进行测试。</span><span class="sxs-lookup"><span data-stu-id="e0222-300">Test with the client app.</span></span> <span data-ttu-id="e0222-301">CORS 故障返回错误，但错误消息不可用到 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="e0222-301">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="e0222-302">在 F12 工具中使用控制台选项卡查看该错误。</span><span class="sxs-lookup"><span data-stu-id="e0222-302">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="e0222-303">具体取决于浏览器中，你收到的错误 （中的 F12 工具控制台） 类似于下面：</span><span class="sxs-lookup"><span data-stu-id="e0222-303">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

  * <span data-ttu-id="e0222-304">使用 Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="e0222-304">Using Microsoft Edge:</span></span>

    <span data-ttu-id="e0222-305">**SEC7120: CORS 原点 'https://localhost:44375未找到 https://localhost:44375中在跨域资源的访问控制的允许的源响应标头 https://webapi.azurewebsites.net/api/values/1。**</span><span class="sxs-lookup"><span data-stu-id="e0222-305">**SEC7120: [CORS] The origin 'https://localhost:44375' did not find 'https://localhost:44375' in the Access-Control-Allow-Origin response header for cross-origin  resource at 'https://webapi.azurewebsites.net/api/values/1'.**</span></span>

  * <span data-ttu-id="e0222-306">使用 Chrome:</span><span class="sxs-lookup"><span data-stu-id="e0222-306">Using Chrome:</span></span>

    <span data-ttu-id="e0222-307">**XMLHttpRequest 在访问 https://webapi.azurewebsites.net/api/values/1来源 https://localhost:44375已阻止的 CORS 策略：请求的资源上存在没有访问控制的允许的域标头。**</span><span class="sxs-lookup"><span data-stu-id="e0222-307">**Access to XMLHttpRequest at 'https://webapi.azurewebsites.net/api/values/1' from origin 'https://localhost:44375' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0222-308">其他资源</span><span class="sxs-lookup"><span data-stu-id="e0222-308">Additional resources</span></span>

* [<span data-ttu-id="e0222-309">跨域资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="e0222-309">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
