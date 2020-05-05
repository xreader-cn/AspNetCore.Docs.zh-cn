---
title: 在 ASP.NET Core 中启用跨域请求（CORS）
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: 6f523a21fe8119c2e4ca4f751ac5b6abc686404b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773806"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="95080-103">在 ASP.NET Core 中启用跨域请求（CORS）</span><span class="sxs-lookup"><span data-stu-id="95080-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="95080-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="95080-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="95080-105">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="95080-106">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="95080-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="95080-107">此限制称为*相同源策略*。</span><span class="sxs-lookup"><span data-stu-id="95080-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="95080-108">同一源策略可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="95080-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="95080-109">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="95080-110">有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="95080-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="95080-111">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="95080-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="95080-112">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="95080-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="95080-113">**不**是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="95080-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="95080-114">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="95080-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="95080-115">有关详细信息，请参阅[CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="95080-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="95080-116">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="95080-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="95080-117">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="95080-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="95080-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="95080-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="95080-119">同一原点</span><span class="sxs-lookup"><span data-stu-id="95080-119">Same origin</span></span>

<span data-ttu-id="95080-120">如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="95080-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="95080-121">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="95080-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="95080-122">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="95080-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="95080-123">`https://example.net`&ndash;不同域</span><span class="sxs-lookup"><span data-stu-id="95080-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="95080-124">`https://www.example.com/foo.html`&ndash;不同子域</span><span class="sxs-lookup"><span data-stu-id="95080-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="95080-125">`http://example.com/foo.html`&ndash;不同方案</span><span class="sxs-lookup"><span data-stu-id="95080-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="95080-126">`https://example.com:9000/foo.html`&ndash;不同端口</span><span class="sxs-lookup"><span data-stu-id="95080-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="95080-127">启用 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-127">Enable CORS</span></span>

<span data-ttu-id="95080-128">有三种方法可启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="95080-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="95080-129">使用[命名策略](#np)或[默认策略](#dp)的中间件。</span><span class="sxs-lookup"><span data-stu-id="95080-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="95080-130">使用[终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="95080-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="95080-131">带有[[EnableCors]](#attr)属性的。</span><span class="sxs-lookup"><span data-stu-id="95080-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="95080-132">通过命名策略使用[[EnableCors]](#attr)属性，可在限制支持 CORS 的终结点时提供最佳控制。</span><span class="sxs-lookup"><span data-stu-id="95080-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="95080-133">以下各节详细介绍了每种方法。</span><span class="sxs-lookup"><span data-stu-id="95080-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="95080-134">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="95080-135">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="95080-136">以下代码将 CORS 策略应用到具有指定来源的所有应用的终结点：</span><span class="sxs-lookup"><span data-stu-id="95080-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="95080-137">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="95080-137">The preceding code:</span></span>

* <span data-ttu-id="95080-138">将策略名称设置为`_myAllowSpecificOrigins`。</span><span class="sxs-lookup"><span data-stu-id="95080-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="95080-139">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="95080-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="95080-140">调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法并指定`_myAllowSpecificOrigins` CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="95080-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="95080-141">`UseCors`添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="95080-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="95080-142">必须将对`UseCors`的调用置于之后`UseRouting`但在之前`UseAuthorization`。</span><span class="sxs-lookup"><span data-stu-id="95080-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="95080-143">有关详细信息，请参阅[中间件顺序](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="95080-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="95080-144">使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="95080-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="95080-145">Lambda 采用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>对象。</span><span class="sxs-lookup"><span data-stu-id="95080-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="95080-146">本文稍后将介绍[配置选项](#cors-policy-options)，如`WithOrigins`。</span><span class="sxs-lookup"><span data-stu-id="95080-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="95080-147">启用所有`_myAllowSpecificOrigins`控制器终结点的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="95080-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="95080-148">请参阅[终结点路由](#ecors)，将 CORS 策略应用到特定终结点。</span><span class="sxs-lookup"><span data-stu-id="95080-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="95080-149">通过终结点路由，CORS 中间件***必须***配置为在对`UseRouting`和`UseEndpoints`的调用之间执行。</span><span class="sxs-lookup"><span data-stu-id="95080-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="95080-150">有关测试代码的说明，请参阅[测试 CORS](#testc) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="95080-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="95080-151"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="95080-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="95080-152">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="95080-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="95080-153">这些<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以链接在一起，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="95080-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="95080-154">注意：指定的 URL**不**能包含尾随斜杠（`/`）。</span><span class="sxs-lookup"><span data-stu-id="95080-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="95080-155">如果 URL 以结尾`/`，则比较返回`false` ，不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="95080-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="95080-156">具有默认策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="95080-157">以下突出显示的代码将启用默认 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="95080-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="95080-158">前面的代码将默认的 CORS 策略应用到所有控制器终结点。</span><span class="sxs-lookup"><span data-stu-id="95080-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="95080-159">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="95080-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="95080-160">目前使用`RequireCors`每个终结点启用 CORS***不支持***[自动预检请求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="95080-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="95080-161">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/20709)和[测试与终结点路由和 [HTTPOPTIONS] 的 CORS](#tcer)。</span><span class="sxs-lookup"><span data-stu-id="95080-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="95080-162">使用终结点路由，可以使用一<xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>组扩展方法在每个终结点上启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="95080-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="95080-163">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="95080-163">In the preceding code:</span></span>

* <span data-ttu-id="95080-164">`app.UseCors`启用 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="95080-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="95080-165">由于尚未配置默认策略， `app.UseCors()`因此单独不启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="95080-166">`/echo`和控制器端点允许使用指定策略的跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="95080-167">`/echo2`和 Pages Razor终结点***不***允许跨源请求，因为未指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="95080-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="95080-168">[[DisableCors]](#dc)特性***不***会禁用通过终结点路由启用的 CORS `RequireCors`。</span><span class="sxs-lookup"><span data-stu-id="95080-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="95080-169">请参阅[测试与终结点路由和 [HttpOptions] 的 CORS](#tcer) ，获取与前面类似的代码测试说明。</span><span class="sxs-lookup"><span data-stu-id="95080-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="95080-170">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-170">Enable CORS with attributes</span></span>

<span data-ttu-id="95080-171">使用[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性启用 CORS，并将命名策略应用到只有那些需要 CORS 的终结点提供了精细的控制。</span><span class="sxs-lookup"><span data-stu-id="95080-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="95080-172">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="95080-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="95080-173">`[EnableCors]`特性启用所选终结点的 CORS，而不是所有终结点：</span><span class="sxs-lookup"><span data-stu-id="95080-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="95080-174">`[EnableCors]`指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="95080-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="95080-175">`[EnableCors("{Policy String}")]`指定命名策略。</span><span class="sxs-lookup"><span data-stu-id="95080-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="95080-176">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="95080-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="95080-177">分页`PageModel`</span><span class="sxs-lookup"><span data-stu-id="95080-177"> Page `PageModel`</span></span>
* <span data-ttu-id="95080-178">控制器</span><span class="sxs-lookup"><span data-stu-id="95080-178">Controller</span></span>
* <span data-ttu-id="95080-179">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="95080-179">Controller action method</span></span>

<span data-ttu-id="95080-180">可以将不同的`[EnableCors]`策略应用到具有属性的控制器、页面模型或操作方法。</span><span class="sxs-lookup"><span data-stu-id="95080-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="95080-181">如果将`[EnableCors]`属性应用于控制器、页面模型或操作方法，并在中间件中启用了 CORS，则会应用***这两种***策略。</span><span class="sxs-lookup"><span data-stu-id="95080-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="95080-182">***建议不要结合策略。使用***特性***或中间件，而不是在同一应用中。*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="95080-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="95080-183">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="95080-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="95080-184">下面的代码创建两个 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="95080-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="95080-185">对于限制 CORS 请求的最佳控制：</span><span class="sxs-lookup"><span data-stu-id="95080-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="95080-186">与`[EnableCors("MyPolicy")]`命名策略一起使用。</span><span class="sxs-lookup"><span data-stu-id="95080-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="95080-187">不要定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="95080-187">Don't define a default policy.</span></span>
* <span data-ttu-id="95080-188">请勿使用[终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="95080-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="95080-189">下一节中的代码满足前面的列表。</span><span class="sxs-lookup"><span data-stu-id="95080-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="95080-190">有关测试代码的说明，请参阅[测试 CORS](#testc) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="95080-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="95080-191">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-191">Disable CORS</span></span>

<span data-ttu-id="95080-192">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) ***特性不会禁用已***通过[终结点路由](#ecors)启用的 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="95080-193">以下代码定义 CORS 策略`"MyPolicy"`：</span><span class="sxs-lookup"><span data-stu-id="95080-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="95080-194">以下代码禁用`GetValues2`操作的 CORS：</span><span class="sxs-lookup"><span data-stu-id="95080-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="95080-195">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="95080-195">The preceding code:</span></span>

* <span data-ttu-id="95080-196">不会使用[终结点路由](#ecors)启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="95080-197">不定义[默认的 CORS 策略](#dp)。</span><span class="sxs-lookup"><span data-stu-id="95080-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="95080-198">使用[[EnableCors （"MyPolicy"）]](#attr)启用控制器的`"MyPolicy"` CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="95080-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="95080-199">为`GetValues2`方法禁用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="95080-200">有关测试上述代码的说明，请参阅[测试 CORS](#testc) 。</span><span class="sxs-lookup"><span data-stu-id="95080-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="95080-201">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="95080-201">CORS policy options</span></span>

<span data-ttu-id="95080-202">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="95080-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="95080-203">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="95080-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="95080-204">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="95080-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="95080-205">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="95080-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="95080-206">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="95080-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="95080-207">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="95080-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="95080-208">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="95080-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="95080-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在中`Startup.ConfigureServices`调用。</span><span class="sxs-lookup"><span data-stu-id="95080-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="95080-210">对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="95080-211">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="95080-211">Set the allowed origins</span></span>

<span data-ttu-id="95080-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;允许所有来源的 CORS 请求和任何方案（`http`或`https`）。</span><span class="sxs-lookup"><span data-stu-id="95080-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="95080-213">`AllowAnyOrigin`是不安全的，因为*任何网站*都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="95080-214">指定`AllowAnyOrigin`和`AllowCredentials`是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="95080-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="95080-215">使用这两种方法配置应用时，CORS 服务将返回无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="95080-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="95080-216">`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。</span><span class="sxs-lookup"><span data-stu-id="95080-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="95080-217">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="95080-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;将策略<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="95080-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="95080-219">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="95080-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="95080-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="95080-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="95080-221">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="95080-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="95080-222">影响预检请求和`Access-Control-Allow-Methods`标头。</span><span class="sxs-lookup"><span data-stu-id="95080-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="95080-223">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="95080-224">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="95080-224">Set the allowed request headers</span></span>

<span data-ttu-id="95080-225">若要允许在 CORS 请求中发送特定标头（称为[作者请求标头](https://xhr.spec.whatwg.org/#request)） <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ，请调用并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="95080-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="95080-226">若要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="95080-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="95080-227">`AllowAnyHeader`影响预检请求和[访问控制请求标](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)头。</span><span class="sxs-lookup"><span data-stu-id="95080-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="95080-228">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="95080-229">仅当发送的标头与中`WithHeaders` `Access-Control-Request-Headers` `WithHeaders`所述的标头完全匹配时，才可以使用 CORS 中间件策略匹配指定的特定标头。</span><span class="sxs-lookup"><span data-stu-id="95080-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="95080-230">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="95080-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="95080-231">CORS 中间件使用以下请求标头拒绝预检请求， `Content-Language`因为（[HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)）未在`WithHeaders`中列出：</span><span class="sxs-lookup"><span data-stu-id="95080-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="95080-232">应用返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="95080-233">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="95080-234">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="95080-234">Set the exposed response headers</span></span>

<span data-ttu-id="95080-235">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="95080-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="95080-236">有关详细信息，请参阅[W3C 跨域资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="95080-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="95080-237">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="95080-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="95080-238">CORS 规范将这些标头称为*简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="95080-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="95080-239">若要使其他标头可用于应用程序<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="95080-240">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="95080-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="95080-241">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="95080-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="95080-242">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="95080-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="95080-243">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="95080-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="95080-244">若要使用跨域请求发送凭据，客户端必须设置`XMLHttpRequest.withCredentials`为。 `true`</span><span class="sxs-lookup"><span data-stu-id="95080-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="95080-245">直接`XMLHttpRequest`使用：</span><span class="sxs-lookup"><span data-stu-id="95080-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="95080-246">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="95080-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="95080-247">使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="95080-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="95080-248">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="95080-248">The server must allow the credentials.</span></span> <span data-ttu-id="95080-249">若要允许跨域凭据，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>调用：</span><span class="sxs-lookup"><span data-stu-id="95080-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="95080-250">HTTP 响应包含一个`Access-Control-Allow-Credentials`标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="95080-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="95080-251">如果浏览器发送凭据，但响应不包含有效`Access-Control-Allow-Credentials`的标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="95080-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="95080-252">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="95080-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="95080-253">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="95080-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="95080-254">CORS 规范还指出，如果标头存在`"*"` ，则将 "源" `Access-Control-Allow-Credentials`设置为 "（所有源）" 无效。</span><span class="sxs-lookup"><span data-stu-id="95080-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="95080-255">预检请求</span><span class="sxs-lookup"><span data-stu-id="95080-255">Preflight requests</span></span>

<span data-ttu-id="95080-256">对于某些 CORS 请求，浏览器会在发出实际请求之前发送额外的[OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)请求。</span><span class="sxs-lookup"><span data-stu-id="95080-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="95080-257">此请求称为[预检请求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。</span><span class="sxs-lookup"><span data-stu-id="95080-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="95080-258">如果满足以下***所有***条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="95080-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="95080-259">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="95080-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="95080-260">应用`Accept`不会设置、 `Accept-Language` `Content-Language` `Content-Type`、、或`Last-Event-ID`以外的请求标头。</span><span class="sxs-lookup"><span data-stu-id="95080-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="95080-261">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="95080-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="95080-262">为客户端请求设置的请求标头上的规则适用于应用通过在`setRequestHeader` `XMLHttpRequest`对象上调用来设置的标头。</span><span class="sxs-lookup"><span data-stu-id="95080-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="95080-263">CORS 规范调用这些标头[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="95080-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="95080-264">此规则不适用于浏览器可以设置的标头，如`User-Agent`、 `Host`或`Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="95080-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="95080-265">下面是一个示例响应，它类似于在本文档的 "[测试 CORS](#testc) " 部分中通过 " **Put test** " 按钮发出的预检请求。</span><span class="sxs-lookup"><span data-stu-id="95080-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="95080-266">预检请求使用[HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)方法。</span><span class="sxs-lookup"><span data-stu-id="95080-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="95080-267">它可能包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="95080-267">It may include the following headers:</span></span>

* <span data-ttu-id="95080-268">[访问控制-请求-方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="95080-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="95080-269">[访问控制-请求标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="95080-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="95080-270">如前文所述，这不包含浏览器设置的标头，如`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="95080-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="95080-271">访问控制-允许-方法</span><span class="sxs-lookup"><span data-stu-id="95080-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="95080-272">如果预检请求被拒绝，应用将返回`200 OK`响应，但不会设置 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="95080-273">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="95080-274">有关拒绝的预检请求的示例，请参阅本文档的[测试 CORS](#testc)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="95080-275">使用 F12 工具时，控制台应用会显示类似于以下内容之一的错误，具体取决于浏览器：</span><span class="sxs-lookup"><span data-stu-id="95080-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="95080-276">Firefox：跨源请求被阻止：相同的源策略不允许读取上`https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`的远程资源。</span><span class="sxs-lookup"><span data-stu-id="95080-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="95080-277">（原因： CORS 请求未成功）。</span><span class="sxs-lookup"><span data-stu-id="95080-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="95080-278">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="95080-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="95080-279">基于 Chromium：派生自源 "https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5https://cors3.azurewebsites.net" 的 "" 访问权限已被 CORS 策略阻止：响应预检请求未通过访问控制检查：请求的资源上没有 "访问控制-允许-源" 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="95080-280">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="95080-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="95080-281">若要允许特定标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="95080-282">若要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="95080-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="95080-283">浏览器的设置`Access-Control-Request-Headers`方式并不一致。</span><span class="sxs-lookup"><span data-stu-id="95080-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="95080-284">如果：</span><span class="sxs-lookup"><span data-stu-id="95080-284">If either:</span></span>

* <span data-ttu-id="95080-285">标头设置为以外的任何内容`"*"`</span><span class="sxs-lookup"><span data-stu-id="95080-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="95080-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>调用：至少`Accept`包含、 `Content-Type`和`Origin`，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="95080-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="95080-287">自动预检请求代码</span><span class="sxs-lookup"><span data-stu-id="95080-287">Automatic preflight request code</span></span>

<span data-ttu-id="95080-288">应用 CORS 策略的时间：</span><span class="sxs-lookup"><span data-stu-id="95080-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="95080-289">通过在中`app.UseCors` `Startup.Configure`调用。</span><span class="sxs-lookup"><span data-stu-id="95080-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="95080-290">使用`[EnableCors]`特性。</span><span class="sxs-lookup"><span data-stu-id="95080-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="95080-291">ASP.NET Core 对 "预检选项" 请求做出响应。</span><span class="sxs-lookup"><span data-stu-id="95080-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="95080-292">目前使用`RequireCors`每个终结点启用 CORS***不支持自动***预检请求。</span><span class="sxs-lookup"><span data-stu-id="95080-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="95080-293">本文档的 "[测试 CORS](#testc) " 部分说明了此行为。</span><span class="sxs-lookup"><span data-stu-id="95080-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="95080-294">用于预检请求的 [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="95080-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="95080-295">如果为 CORS 启用了适当的策略，ASP.NET Core 通常会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="95080-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="95080-296">在某些情况下，可能不会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="95080-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="95080-297">例如，将[CORS 用于终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="95080-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="95080-298">下面的代码使用[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)特性为 OPTIONS 请求创建终结点：</span><span class="sxs-lookup"><span data-stu-id="95080-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="95080-299">有关测试上述代码的说明，请参阅[通过终结点路由测试 CORS 和 [HttpOptions]](#tcer) 。</span><span class="sxs-lookup"><span data-stu-id="95080-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="95080-300">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="95080-300">Set the preflight expiration time</span></span>

<span data-ttu-id="95080-301">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="95080-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="95080-302">若要设置此标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="95080-303">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="95080-303">How CORS works</span></span>

<span data-ttu-id="95080-304">本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="95080-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="95080-305">CORS**不**是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="95080-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="95080-306">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="95080-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="95080-307">例如，恶意执行组件可能对站点使用[跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求来窃取信息。</span><span class="sxs-lookup"><span data-stu-id="95080-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="95080-308">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="95080-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="95080-309">它由客户端（浏览器）来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="95080-310">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="95080-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="95080-311">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="95080-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="95080-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="95080-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="95080-313">Postman</span><span class="sxs-lookup"><span data-stu-id="95080-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="95080-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="95080-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="95080-315">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="95080-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="95080-316">这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="95080-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="95080-317">没有 CORS 的浏览器不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="95080-318">在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="95080-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="95080-319">JSONP 不使用 XHR，而是使用`<script>`标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="95080-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="95080-320">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="95080-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="95080-321">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="95080-322">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="95080-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="95080-323">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="95080-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="95080-324">部署的[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的 " [PUT 测试" 按钮](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="95080-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="95080-325">下面是一个从 "[值](https://cors3.azurewebsites.net/)" 测试按钮到`https://cors1.azurewebsites.net/api/values`的跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="95080-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="95080-326">`Origin`标头：</span><span class="sxs-lookup"><span data-stu-id="95080-326">The `Origin` header:</span></span>

* <span data-ttu-id="95080-327">提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="95080-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="95080-328">是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="95080-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="95080-329">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="95080-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="95080-330">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="95080-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="95080-331">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="95080-331">**Request headers**</span></span>

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

<span data-ttu-id="95080-332">在`OPTIONS`请求中，服务器设置响应中的**响应标头** `Access-Control-Allow-Origin: {allowed origin}`标头。</span><span class="sxs-lookup"><span data-stu-id="95080-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="95080-333">例如，已部署的[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS`请求包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="95080-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="95080-334">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="95080-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="95080-335">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="95080-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="95080-336">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="95080-336">**Request headers**</span></span>

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

<span data-ttu-id="95080-337">在前面的**响应标头**中，服务器设置响应中的[访问控制允许源](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)标头。</span><span class="sxs-lookup"><span data-stu-id="95080-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="95080-338">此`https://cors1.azurewebsites.net`标头的值与请求`Origin`中的标头相匹配。</span><span class="sxs-lookup"><span data-stu-id="95080-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="95080-339">如果<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>调用了， `Access-Control-Allow-Origin: *`则将返回通配符值。</span><span class="sxs-lookup"><span data-stu-id="95080-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="95080-340">`AllowAnyOrigin`允许任何源。</span><span class="sxs-lookup"><span data-stu-id="95080-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="95080-341">如果响应不包含`Access-Control-Allow-Origin`标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="95080-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="95080-342">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="95080-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="95080-343">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="95080-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="95080-344">显示选项请求</span><span class="sxs-lookup"><span data-stu-id="95080-344">Display OPTIONS requests</span></span>

<span data-ttu-id="95080-345">默认情况下，Chrome 和 Edge 浏览器不会在 F12 工具的 "网络" 选项卡上显示 "请求" 选项。</span><span class="sxs-lookup"><span data-stu-id="95080-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="95080-346">若要在这些浏览器中显示选项请求：</span><span class="sxs-lookup"><span data-stu-id="95080-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="95080-347">`chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="95080-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="95080-348">禁用标志。</span><span class="sxs-lookup"><span data-stu-id="95080-348">disable the flag.</span></span>
* <span data-ttu-id="95080-349">重新启动.</span><span class="sxs-lookup"><span data-stu-id="95080-349">restart.</span></span>

<span data-ttu-id="95080-350">默认情况下，Firefox 显示 "选项请求"。</span><span class="sxs-lookup"><span data-stu-id="95080-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="95080-351">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-351">CORS in IIS</span></span>

<span data-ttu-id="95080-352">部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="95080-353">若要支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。</span><span class="sxs-lookup"><span data-stu-id="95080-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="95080-354">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-354">Test CORS</span></span>

<span data-ttu-id="95080-355">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)包含测试 CORS 的代码。</span><span class="sxs-lookup"><span data-stu-id="95080-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="95080-356">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="95080-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="95080-357">该示例是一个 API 项目， Razor其中添加了页面：</span><span class="sxs-lookup"><span data-stu-id="95080-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="95080-358">`WithOrigins("https://localhost:<port>");`应仅用于测试示例应用程序，类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="95080-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="95080-359">下面`ValuesController`提供用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="95080-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="95080-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)由[RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 包提供，并显示路由信息。</span><span class="sxs-lookup"><span data-stu-id="95080-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="95080-361">使用以下方法之一测试前面的示例代码：</span><span class="sxs-lookup"><span data-stu-id="95080-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="95080-362">使用部署的示例应用[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)。</span><span class="sxs-lookup"><span data-stu-id="95080-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="95080-363">无需下载示例。</span><span class="sxs-lookup"><span data-stu-id="95080-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="95080-364">`dotnet run`使用的默认 URL 运行示例`https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="95080-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="95080-365">从 Visual Studio 运行示例，并将端口设置为44398，查找的 URL `https://localhost:44398`。</span><span class="sxs-lookup"><span data-stu-id="95080-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="95080-366">使用带有 F12 工具的浏览器：</span><span class="sxs-lookup"><span data-stu-id="95080-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="95080-367">选择 "**值**" 按钮，然后查看 "**网络**" 选项卡中的标头。</span><span class="sxs-lookup"><span data-stu-id="95080-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="95080-368">选择 "**放置测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="95080-368">Select the **PUT test** button.</span></span> <span data-ttu-id="95080-369">请参阅[显示选项请求](#options)，以获取有关显示选项请求的说明。</span><span class="sxs-lookup"><span data-stu-id="95080-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="95080-370">**PUT 测试**创建两个请求：一个选项预检请求和 PUT 请求。</span><span class="sxs-lookup"><span data-stu-id="95080-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="95080-371">选择此**`GetValues2 [DisableCors]`** 按钮可触发失败的 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="95080-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="95080-372">如文档中所述，响应返回200成功，但不进行 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="95080-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="95080-373">选择 "**控制台**" 选项卡以查看 CORS 错误。</span><span class="sxs-lookup"><span data-stu-id="95080-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="95080-374">根据浏览器，将显示类似于以下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="95080-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="95080-375">CORS 策略已阻止`'https://cors1.azurewebsites.net/api/values/GetValues2'`从原始`'https://cors3.azurewebsites.net'`位置获取的访问权限：请求的资源上没有 "访问控制-允许" 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="95080-376">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="95080-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="95080-377">可以使用[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)等工具来测试启用了[CORS 的终结](https://curl.haxx.se/)点。</span><span class="sxs-lookup"><span data-stu-id="95080-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="95080-378">使用工具时， `Origin`标头指定的请求源必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="95080-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="95080-379">如果请求不是*cross-origin*基于`Origin`标头值跨域的，则：</span><span class="sxs-lookup"><span data-stu-id="95080-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="95080-380">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="95080-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="95080-381">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="95080-382">以下命令使用`curl`发出带有以下信息的选项请求：</span><span class="sxs-lookup"><span data-stu-id="95080-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="95080-383">通过终结点路由和 [HttpOptions] 测试 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="95080-384">目前使用`RequireCors`每个终结点启用 CORS***不支持***[自动预检请求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="95080-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="95080-385">请考虑以下代码，它使用[终结点路由启用 CORS](#ecors)：</span><span class="sxs-lookup"><span data-stu-id="95080-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="95080-386">下面`TodoItems1Controller`提供用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="95080-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="95080-387">从已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[测试页](https://cors1.azurewebsites.net/test?number=1)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="95080-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="95080-388">**Delete [EnableCors]** 和**GET [EnableCors]** 按钮成功，因为终结点具有`[EnableCors]`和响应预检请求。</span><span class="sxs-lookup"><span data-stu-id="95080-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="95080-389">其他终结点失败。</span><span class="sxs-lookup"><span data-stu-id="95080-389">The other endpoints fails.</span></span> <span data-ttu-id="95080-390">"**获取**" 按钮失败，因为[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)发送：</span><span class="sxs-lookup"><span data-stu-id="95080-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="95080-391">下面`TodoItems2Controller`提供了类似的终结点，但包含响应选项请求的显式代码：</span><span class="sxs-lookup"><span data-stu-id="95080-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="95080-392">从已部署示例的[测试页](https://cors1.azurewebsites.net/test?number=2)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="95080-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="95080-393">在 "**控制器**" 下拉列表中，选择 "**预检**"，然后**设置 "控制器**"。</span><span class="sxs-lookup"><span data-stu-id="95080-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="95080-394">对`TodoItems2Controller`终结点的所有 CORS 调用都将成功。</span><span class="sxs-lookup"><span data-stu-id="95080-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95080-395">其他资源</span><span class="sxs-lookup"><span data-stu-id="95080-395">Additional resources</span></span>

* [<span data-ttu-id="95080-396">跨源资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="95080-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="95080-397">IIS CORS 模块入门</span><span class="sxs-lookup"><span data-stu-id="95080-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95080-398">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="95080-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="95080-399">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="95080-400">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="95080-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="95080-401">此限制称为*相同源策略*。</span><span class="sxs-lookup"><span data-stu-id="95080-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="95080-402">同一源策略可防止恶意站点读取另一个站点中的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="95080-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="95080-403">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="95080-404">有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="95080-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="95080-405">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="95080-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="95080-406">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="95080-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="95080-407">**不**是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="95080-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="95080-408">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="95080-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="95080-409">有关详细信息，请参阅[CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="95080-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="95080-410">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="95080-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="95080-411">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="95080-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="95080-412">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="95080-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="95080-413">同一原点</span><span class="sxs-lookup"><span data-stu-id="95080-413">Same origin</span></span>

<span data-ttu-id="95080-414">如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="95080-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="95080-415">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="95080-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="95080-416">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="95080-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="95080-417">`https://example.net`&ndash;不同域</span><span class="sxs-lookup"><span data-stu-id="95080-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="95080-418">`https://www.example.com/foo.html`&ndash;不同子域</span><span class="sxs-lookup"><span data-stu-id="95080-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="95080-419">`http://example.com/foo.html`&ndash;不同方案</span><span class="sxs-lookup"><span data-stu-id="95080-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="95080-420">`https://example.com:9000/foo.html`&ndash;不同端口</span><span class="sxs-lookup"><span data-stu-id="95080-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="95080-421">比较来源时，Internet Explorer 不会考虑该端口。</span><span class="sxs-lookup"><span data-stu-id="95080-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="95080-422">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="95080-423">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="95080-424">以下代码通过指定源为整个应用启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="95080-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="95080-425">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="95080-425">The preceding code:</span></span>

* <span data-ttu-id="95080-426">将策略名称设置为 "\_myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="95080-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="95080-427">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="95080-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="95080-428">调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法，该方法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="95080-429">使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="95080-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="95080-430">Lambda 采用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>对象。</span><span class="sxs-lookup"><span data-stu-id="95080-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="95080-431">本文稍后将介绍[配置选项](#cors-policy-options)，如`WithOrigins`。</span><span class="sxs-lookup"><span data-stu-id="95080-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="95080-432"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="95080-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="95080-433">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="95080-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="95080-434"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以链接方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="95080-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="95080-435">注意： URL**不得包含尾随**斜杠（`/`）。</span><span class="sxs-lookup"><span data-stu-id="95080-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="95080-436">如果 URL 以结尾`/`，则比较返回`false` ，不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="95080-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="95080-437">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="95080-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="95080-438">注意： `UseCors`必须先`UseMvc`调用。</span><span class="sxs-lookup"><span data-stu-id="95080-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="95080-439">请参阅[在页面Razor 、控制器和操作方法中启用 cors，](#ecors)以在页面/控制器/操作级别应用 cors 策略。</span><span class="sxs-lookup"><span data-stu-id="95080-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="95080-440">有关测试代码的说明，请参阅[测试 CORS](#test) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="95080-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="95080-441">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-441">Enable CORS with attributes</span></span>

<span data-ttu-id="95080-442">EnableCors 属性提供了一种用于全局应用 CORS 的替代方法。 [ &lbrack;&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="95080-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="95080-443">`[EnableCors]`属性为所选终结点启用 CORS，而不是所有终结点。</span><span class="sxs-lookup"><span data-stu-id="95080-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="95080-444">使用`[EnableCors]`指定默认策略并`[EnableCors("{Policy String}")]`指定策略。</span><span class="sxs-lookup"><span data-stu-id="95080-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="95080-445">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="95080-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="95080-446">分页`PageModel`</span><span class="sxs-lookup"><span data-stu-id="95080-446"> Page `PageModel`</span></span>
* <span data-ttu-id="95080-447">控制器</span><span class="sxs-lookup"><span data-stu-id="95080-447">Controller</span></span>
* <span data-ttu-id="95080-448">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="95080-448">Controller action method</span></span>

<span data-ttu-id="95080-449">您可以使用`[EnableCors]`属性将不同的策略应用到控制器/页模型/操作。</span><span class="sxs-lookup"><span data-stu-id="95080-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="95080-450">如果将`[EnableCors]`属性应用于控制器/页面模型/操作方法，并在中间件中启用了 CORS，则会应用***这两种***策略。</span><span class="sxs-lookup"><span data-stu-id="95080-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="95080-451">建议***不要***合并策略。</span><span class="sxs-lookup"><span data-stu-id="95080-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="95080-452">使用`[EnableCors]`特性或中间件，**而不能同时使用两者**。</span><span class="sxs-lookup"><span data-stu-id="95080-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="95080-453">使用`[EnableCors]`时 **，请不要定义默认**策略。</span><span class="sxs-lookup"><span data-stu-id="95080-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="95080-454">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="95080-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="95080-455">以下代码创建 CORS 默认策略和名为`"AnotherPolicy"`的策略：</span><span class="sxs-lookup"><span data-stu-id="95080-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="95080-456">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-456">Disable CORS</span></span>

<span data-ttu-id="95080-457">DisableCors 属性为控制器/页模型/操作禁用 CORS。 [ &lbrack;&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="95080-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="95080-458">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="95080-458">CORS policy options</span></span>

<span data-ttu-id="95080-459">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="95080-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="95080-460">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="95080-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="95080-461">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="95080-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="95080-462">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="95080-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="95080-463">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="95080-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="95080-464">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="95080-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="95080-465">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="95080-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="95080-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在中`Startup.ConfigureServices`调用。</span><span class="sxs-lookup"><span data-stu-id="95080-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="95080-467">对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="95080-468">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="95080-468">Set the allowed origins</span></span>

<span data-ttu-id="95080-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;允许所有来源的 CORS 请求和任何方案（`http`或`https`）。</span><span class="sxs-lookup"><span data-stu-id="95080-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="95080-470">`AllowAnyOrigin`是不安全的，因为*任何网站*都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="95080-471">指定`AllowAnyOrigin`和`AllowCredentials`是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="95080-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="95080-472">对于安全应用，如果客户端必须授权自身访问服务器资源，请指定准确的来源列表。</span><span class="sxs-lookup"><span data-stu-id="95080-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="95080-473">`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。</span><span class="sxs-lookup"><span data-stu-id="95080-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="95080-474">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="95080-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;将策略<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="95080-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="95080-476">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="95080-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="95080-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="95080-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="95080-478">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="95080-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="95080-479">影响预检请求和`Access-Control-Allow-Methods`标头。</span><span class="sxs-lookup"><span data-stu-id="95080-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="95080-480">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="95080-481">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="95080-481">Set the allowed request headers</span></span>

<span data-ttu-id="95080-482">若要允许在 CORS 请求中发送特定标头（称为*作者请求标头*） <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ，请调用并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="95080-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="95080-483">若要允许所有作者请求标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="95080-484">此设置会影响预检请求和`Access-Control-Request-Headers`标头。</span><span class="sxs-lookup"><span data-stu-id="95080-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="95080-485">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="95080-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="95080-486">CORS 中间件始终允许发送四个`Access-Control-Request-Headers`标头，而不考虑在 CorsPolicy 中配置的值。</span><span class="sxs-lookup"><span data-stu-id="95080-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="95080-487">此标头列表包括：</span><span class="sxs-lookup"><span data-stu-id="95080-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="95080-488">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="95080-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="95080-489">CORS 中间件使用以下请求标头成功响应了预检请求， `Content-Language`因为始终为白名单：</span><span class="sxs-lookup"><span data-stu-id="95080-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="95080-490">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="95080-490">Set the exposed response headers</span></span>

<span data-ttu-id="95080-491">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="95080-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="95080-492">有关详细信息，请参阅[W3C 跨域资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="95080-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="95080-493">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="95080-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="95080-494">CORS 规范将这些标头称为*简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="95080-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="95080-495">若要使其他标头可用于应用程序<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="95080-496">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="95080-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="95080-497">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="95080-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="95080-498">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="95080-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="95080-499">凭据包括 cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="95080-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="95080-500">若要使用跨域请求发送凭据，客户端必须设置`XMLHttpRequest.withCredentials`为。 `true`</span><span class="sxs-lookup"><span data-stu-id="95080-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="95080-501">直接`XMLHttpRequest`使用：</span><span class="sxs-lookup"><span data-stu-id="95080-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="95080-502">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="95080-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="95080-503">使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="95080-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="95080-504">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="95080-504">The server must allow the credentials.</span></span> <span data-ttu-id="95080-505">若要允许跨域凭据，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>调用：</span><span class="sxs-lookup"><span data-stu-id="95080-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="95080-506">HTTP 响应包含一个`Access-Control-Allow-Credentials`标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="95080-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="95080-507">如果浏览器发送凭据，但响应不包含有效`Access-Control-Allow-Credentials`的标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="95080-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="95080-508">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="95080-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="95080-509">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="95080-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="95080-510">CORS 规范还指出，如果标头存在`"*"` ，则将 "源" `Access-Control-Allow-Credentials`设置为 "（所有源）" 无效。</span><span class="sxs-lookup"><span data-stu-id="95080-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="95080-511">预检请求</span><span class="sxs-lookup"><span data-stu-id="95080-511">Preflight requests</span></span>

<span data-ttu-id="95080-512">对于某些 CORS 请求，浏览器会在发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="95080-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="95080-513">此请求称为*预检请求*。</span><span class="sxs-lookup"><span data-stu-id="95080-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="95080-514">如果满足以下条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="95080-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="95080-515">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="95080-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="95080-516">应用`Accept`不会设置、 `Accept-Language` `Content-Language` `Content-Type`、、或`Last-Event-ID`以外的请求标头。</span><span class="sxs-lookup"><span data-stu-id="95080-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="95080-517">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="95080-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="95080-518">为客户端请求设置的请求标头上的规则适用于应用通过在`setRequestHeader` `XMLHttpRequest`对象上调用来设置的标头。</span><span class="sxs-lookup"><span data-stu-id="95080-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="95080-519">CORS 规范调用这些标头*作者请求标头*。</span><span class="sxs-lookup"><span data-stu-id="95080-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="95080-520">此规则不适用于浏览器可以设置的标头，如`User-Agent`、 `Host`或`Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="95080-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="95080-521">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="95080-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="95080-522">预航班请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="95080-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="95080-523">它包括两个特殊标头：</span><span class="sxs-lookup"><span data-stu-id="95080-523">It includes two special headers:</span></span>

* <span data-ttu-id="95080-524">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="95080-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="95080-525">`Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="95080-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="95080-526">如前文所述，这不包含浏览器设置的标头，如`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="95080-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="95080-527">如果为 CORS 启用了适当的策略，ASP.NET Core 一般会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="95080-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="95080-528">请参阅[[HttpOptions] 属性以获取预检请求](#pro)。</span><span class="sxs-lookup"><span data-stu-id="95080-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="95080-529">CORS 预检请求可能包含一个`Access-Control-Request-Headers`标头，该标头向服务器指示与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="95080-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="95080-530">若要允许特定标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="95080-531">若要允许所有作者请求标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="95080-532">浏览器在设置`Access-Control-Request-Headers`方式上并不完全一致。</span><span class="sxs-lookup"><span data-stu-id="95080-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="95080-533">如果将标`"*"`头设置为（或使用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>）以外的任何内容，则应该至少`Accept`包含`Content-Type`、、 `Origin`和，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="95080-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="95080-534">下面是针对预检请求的示例响应（假定服务器允许该请求）：</span><span class="sxs-lookup"><span data-stu-id="95080-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="95080-535">响应包含一个`Access-Control-Allow-Methods`标头，其中列出了允许的方法， `Access-Control-Allow-Headers`还可以选择标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="95080-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="95080-536">如果预检请求成功，则浏览器发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="95080-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="95080-537">如果预检请求被拒绝，应用将返回*200 OK*响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="95080-538">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="95080-539">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="95080-539">Set the preflight expiration time</span></span>

<span data-ttu-id="95080-540">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="95080-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="95080-541">若要设置此标头<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>，请调用：</span><span class="sxs-lookup"><span data-stu-id="95080-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="95080-542">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="95080-542">How CORS works</span></span>

<span data-ttu-id="95080-543">本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="95080-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="95080-544">CORS**不**是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="95080-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="95080-545">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="95080-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="95080-546">例如，恶意执行组件可能会对站点使用[阻止跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求，以窃取信息。</span><span class="sxs-lookup"><span data-stu-id="95080-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="95080-547">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="95080-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="95080-548">它由客户端（浏览器）来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="95080-549">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="95080-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="95080-550">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="95080-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="95080-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="95080-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="95080-552">Postman</span><span class="sxs-lookup"><span data-stu-id="95080-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="95080-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="95080-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="95080-554">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="95080-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="95080-555">这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="95080-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="95080-556">浏览器（不含 CORS）不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="95080-557">在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="95080-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="95080-558">JSONP 不使用 XHR，而是使用`<script>`标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="95080-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="95080-559">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="95080-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="95080-560">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="95080-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="95080-561">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="95080-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="95080-562">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="95080-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="95080-563">下面是一个跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="95080-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="95080-564">`Origin`标头提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="95080-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="95080-565">标`Origin`头是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="95080-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="95080-566">如果服务器允许该请求，则会在响应`Access-Control-Allow-Origin`中设置标头。</span><span class="sxs-lookup"><span data-stu-id="95080-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="95080-567">此标头的值可以与请求`Origin`中的标头相匹配，也可以`"*"`是通配符值，这意味着允许任何源：</span><span class="sxs-lookup"><span data-stu-id="95080-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="95080-568">如果响应不包含`Access-Control-Allow-Origin`标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="95080-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="95080-569">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="95080-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="95080-570">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="95080-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="95080-571">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-571">Test CORS</span></span>

<span data-ttu-id="95080-572">测试 CORS：</span><span class="sxs-lookup"><span data-stu-id="95080-572">To test CORS:</span></span>

1. <span data-ttu-id="95080-573">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="95080-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="95080-574">或者，您也可以[下载该示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="95080-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="95080-575">使用本文档中的方法之一启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="95080-576">例如：</span><span class="sxs-lookup"><span data-stu-id="95080-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="95080-577">`WithOrigins("https://localhost:<port>");`应仅用于测试示例应用程序，类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="95080-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="95080-578">创建 web 应用项目（Razor页或 MVC）。</span><span class="sxs-lookup"><span data-stu-id="95080-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="95080-579">该示例使用Razor页面。</span><span class="sxs-lookup"><span data-stu-id="95080-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="95080-580">可以在与 API 项目相同的解决方案中创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="95080-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="95080-581">将以下突出显示的代码添加到*索引 cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="95080-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="95080-582">在上面的代码中， `url: 'https://<web app>.azurewebsites.net/api/values/1',`将替换为已部署应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="95080-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="95080-583">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="95080-583">Deploy the API project.</span></span> <span data-ttu-id="95080-584">例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="95080-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="95080-585">从桌面Razor运行页面或 MVC 应用，并单击 "**测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="95080-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="95080-586">使用 F12 工具查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="95080-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="95080-587">从中`WithOrigins`删除 localhost 源并部署应用。</span><span class="sxs-lookup"><span data-stu-id="95080-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="95080-588">或者，使用其他端口运行客户端应用。</span><span class="sxs-lookup"><span data-stu-id="95080-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="95080-589">例如，在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="95080-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="95080-590">与客户端应用程序进行测试。</span><span class="sxs-lookup"><span data-stu-id="95080-590">Test with the client app.</span></span> <span data-ttu-id="95080-591">CORS 故障返回一个错误，但错误消息不能用于 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="95080-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="95080-592">使用 F12 工具中的 "控制台" 选项卡查看错误。</span><span class="sxs-lookup"><span data-stu-id="95080-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="95080-593">根据浏览器，你会收到类似于以下内容的错误（在 F12 工具控制台中）：</span><span class="sxs-lookup"><span data-stu-id="95080-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="95080-594">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="95080-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="95080-595">**SEC7120： [CORS] 源`https://localhost:44375`在跨域资源`https://localhost:44375`的访问控制允许源响应标头中找不到`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="95080-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="95080-596">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="95080-596">Using Chrome:</span></span>

     <span data-ttu-id="95080-597">**CORS 策略已阻止`https://webapi.azurewebsites.net/api/values/1`从原始`https://localhost:44375`位置访问 XMLHttpRequest：请求的资源上没有 "访问控制-允许" 标头。**</span><span class="sxs-lookup"><span data-stu-id="95080-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="95080-598">可以使用工具（如[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)）测试启用 CORS 的终结点。</span><span class="sxs-lookup"><span data-stu-id="95080-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="95080-599">使用工具时， `Origin`标头指定的请求源必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="95080-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="95080-600">如果请求不是*cross-origin*基于`Origin`标头值跨域的，则：</span><span class="sxs-lookup"><span data-stu-id="95080-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="95080-601">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="95080-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="95080-602">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="95080-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="95080-603">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="95080-603">CORS in IIS</span></span>

<span data-ttu-id="95080-604">部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。</span><span class="sxs-lookup"><span data-stu-id="95080-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="95080-605">若要支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。</span><span class="sxs-lookup"><span data-stu-id="95080-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="95080-606">其他资源</span><span class="sxs-lookup"><span data-stu-id="95080-606">Additional resources</span></span>

* [<span data-ttu-id="95080-607">跨源资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="95080-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="95080-608">IIS CORS 模块入门</span><span class="sxs-lookup"><span data-stu-id="95080-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
