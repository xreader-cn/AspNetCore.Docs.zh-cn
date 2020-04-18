---
title: 在 ASP.NET 核心中启用跨源请求 （CORS）
author: rick-anderson
description: 了解 CORS 如何作为在 ASP.NET 酷应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642699"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="531c7-103">在 ASP.NET 核心中启用跨源请求 （CORS）</span><span class="sxs-lookup"><span data-stu-id="531c7-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="531c7-104">由[里克·安德森](https://twitter.com/RickAndMSFT)和[柯克·拉金](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="531c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="531c7-105">本文演示如何在ASP.NET核心应用中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="531c7-106">浏览器安全性可防止网页向与服务网页的域不同的域发出请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="531c7-107">此限制称为*同源策略*。</span><span class="sxs-lookup"><span data-stu-id="531c7-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="531c7-108">同源策略可防止恶意站点从其他站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="531c7-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="531c7-109">有时，您可能希望允许其他网站向你的应用发出交叉源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="531c7-110">有关详细信息，请参阅[Mozilla CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="531c7-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="531c7-111">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="531c7-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="531c7-112">是允许服务器放松同源策略的 W3C 标准。</span><span class="sxs-lookup"><span data-stu-id="531c7-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="531c7-113">**CORS 不是**安全功能，可放松安全性。</span><span class="sxs-lookup"><span data-stu-id="531c7-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="531c7-114">通过允许 CORS，API 并不更安全。</span><span class="sxs-lookup"><span data-stu-id="531c7-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="531c7-115">有关详细信息，请参阅[CORS 的工作原理](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="531c7-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="531c7-116">允许服务器显式允许某些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="531c7-117">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全、更灵活。</span><span class="sxs-lookup"><span data-stu-id="531c7-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="531c7-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="531c7-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="531c7-119">同一源</span><span class="sxs-lookup"><span data-stu-id="531c7-119">Same origin</span></span>

<span data-ttu-id="531c7-120">如果两个 URL 具有相同的方案、主机和端口[（RFC 6454），](https://tools.ietf.org/html/rfc6454)则它们具有相同的源源。</span><span class="sxs-lookup"><span data-stu-id="531c7-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="531c7-121">这两个 URL 具有相同的来源：</span><span class="sxs-lookup"><span data-stu-id="531c7-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="531c7-122">这些 URL 与前两个 URL 具有不同的来源：</span><span class="sxs-lookup"><span data-stu-id="531c7-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="531c7-123">`https://example.net`&ndash;不同的域</span><span class="sxs-lookup"><span data-stu-id="531c7-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="531c7-124">`https://www.example.com/foo.html`&ndash;不同的子域</span><span class="sxs-lookup"><span data-stu-id="531c7-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="531c7-125">`http://example.com/foo.html`&ndash;不同的方案</span><span class="sxs-lookup"><span data-stu-id="531c7-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="531c7-126">`https://example.com:9000/foo.html`&ndash;不同的端口</span><span class="sxs-lookup"><span data-stu-id="531c7-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="531c7-127">启用 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-127">Enable CORS</span></span>

<span data-ttu-id="531c7-128">有三种方法可以启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="531c7-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="531c7-129">在中间件中使用[命名策略](#np)或[默认策略](#dp)。</span><span class="sxs-lookup"><span data-stu-id="531c7-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="531c7-130">使用[终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="531c7-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="531c7-131">使用[[启用 Cors]](#attr)属性。</span><span class="sxs-lookup"><span data-stu-id="531c7-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="531c7-132">将[[EnableCors]](#attr)属性与命名策略一起使用，可在限制支持 CORS 的终结点时提供最佳控制。</span><span class="sxs-lookup"><span data-stu-id="531c7-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="531c7-133">每种方法都详见以下各节。</span><span class="sxs-lookup"><span data-stu-id="531c7-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="531c7-134">带有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="531c7-135">CORS 中间件处理跨源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="531c7-136">以下代码将 CORS 策略应用于具有指定来源的所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="531c7-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="531c7-137">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="531c7-137">The preceding code:</span></span>

* <span data-ttu-id="531c7-138">将策略名称设置到`_myAllowSpecificOrigins`。</span><span class="sxs-lookup"><span data-stu-id="531c7-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="531c7-139">策略名称是任意的。</span><span class="sxs-lookup"><span data-stu-id="531c7-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="531c7-140">调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法并指定`_myAllowSpecificOrigins`CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="531c7-141">`UseCors`添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="531c7-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="531c7-142">调用 后`UseCors`必须发出`UseRouting`，但之前`UseAuthorization`。</span><span class="sxs-lookup"><span data-stu-id="531c7-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="531c7-143">有关详细信息，请参阅[中间件订单](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="531c7-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="531c7-144">使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的调用。</span><span class="sxs-lookup"><span data-stu-id="531c7-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="531c7-145">lambda 取一<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>个对象。</span><span class="sxs-lookup"><span data-stu-id="531c7-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="531c7-146">[配置选项](#cors-policy-options)（如`WithOrigins`）在本文的后面部分介绍。</span><span class="sxs-lookup"><span data-stu-id="531c7-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="531c7-147">为所有控制器`_myAllowSpecificOrigins`终结点启用 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="531c7-148">请参阅[终结点路由](#ecors)，将 CORS 策略应用于特定终结点。</span><span class="sxs-lookup"><span data-stu-id="531c7-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="531c7-149">使用端点路由时，***必须***将 CORS 中间件配置为在调用`UseRouting`和`UseEndpoints`之间执行。</span><span class="sxs-lookup"><span data-stu-id="531c7-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="531c7-150">有关测试代码的说明，请参阅[测试 CORS，](#testc)这些说明与前面的代码类似。</span><span class="sxs-lookup"><span data-stu-id="531c7-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="531c7-151">方法<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="531c7-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="531c7-152">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="531c7-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="531c7-153">这些方法<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>可以链接，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="531c7-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="531c7-154">注意： 指定的 URL**不能**包含尾随斜杠`/`（ 。</span><span class="sxs-lookup"><span data-stu-id="531c7-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="531c7-155">如果 URL 终止与`/`，则比较`false`返回，并且不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="531c7-156">具有默认策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="531c7-157">以下突出显示的代码启用默认 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="531c7-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="531c7-158">前面的代码将默认的 CORS 策略应用于所有控制器终结点。</span><span class="sxs-lookup"><span data-stu-id="531c7-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="531c7-159">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="531c7-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="531c7-160">使用`RequireCors`当前基于每个终结点启用 CORS***不支持***[自动预检请求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="531c7-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="531c7-161">有关详细信息，请参阅此[GitHub 问题和](https://github.com/dotnet/aspnetcore/issues/20709)[使用终结点路由和 [HttpOptions] 测试 CORS。](#tcer)</span><span class="sxs-lookup"><span data-stu-id="531c7-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="531c7-162">使用端点路由，可以使用扩展方法<xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>集，按终结点启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="531c7-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="531c7-163">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="531c7-163">In the preceding code:</span></span>

* <span data-ttu-id="531c7-164">`app.UseCors`启用 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="531c7-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="531c7-165">由于尚未配置默认策略，`app.UseCors()`因此单独无法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="531c7-166">`/echo`和 控制器终结点允许使用指定的策略进行交叉源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="531c7-167">`/echo2`和 Razor Pages 终结点***不允许***跨源请求，因为未指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="531c7-168">[[禁用 Cors]](#dc)属性***不会***禁用由 终结点路由启用的`RequireCors`CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="531c7-169">有关测试代码的指令，请参阅[使用端点路由和 [HttpOptions] 测试 CORS。](#tcer)</span><span class="sxs-lookup"><span data-stu-id="531c7-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="531c7-170">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-170">Enable CORS with attributes</span></span>

<span data-ttu-id="531c7-171">使用[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性启用 CORS 并将命名策略应用于仅对需要 CORS 的终结点提供最佳控制。</span><span class="sxs-lookup"><span data-stu-id="531c7-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="531c7-172">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="531c7-173">该`[EnableCors]`属性为选定的终结点启用 CORS，而不是所有终结点：</span><span class="sxs-lookup"><span data-stu-id="531c7-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="531c7-174">`[EnableCors]`指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="531c7-175">`[EnableCors("{Policy String}")]`指定命名策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="531c7-176">该`[EnableCors]`属性可应用于：</span><span class="sxs-lookup"><span data-stu-id="531c7-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="531c7-177">剃刀页面`PageModel`</span><span class="sxs-lookup"><span data-stu-id="531c7-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="531c7-178">控制器</span><span class="sxs-lookup"><span data-stu-id="531c7-178">Controller</span></span>
* <span data-ttu-id="531c7-179">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="531c7-179">Controller action method</span></span>

<span data-ttu-id="531c7-180">可以将不同的策略应用于具有属性的`[EnableCors]`控制器、页面模型或操作方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="531c7-181">当该`[EnableCors]`属性应用于控制器、页面模型或操作方法，并在中间件中启用 CORS 时，将应用***这两个***策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="531c7-182">***我们建议不要合并策略。使用相同的应用程序的属性***`[EnableCors]`***或中间件，而不是两者在同一应用中。***</span><span class="sxs-lookup"><span data-stu-id="531c7-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="531c7-183">以下代码对每种方法应用不同的策略：</span><span class="sxs-lookup"><span data-stu-id="531c7-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="531c7-184">以下代码创建两个 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="531c7-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="531c7-185">为了最好地控制限制 CORS 请求：</span><span class="sxs-lookup"><span data-stu-id="531c7-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="531c7-186">与`[EnableCors("MyPolicy")]`命名策略一起使用。</span><span class="sxs-lookup"><span data-stu-id="531c7-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="531c7-187">不要定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-187">Don't define a default policy.</span></span>
* <span data-ttu-id="531c7-188">不要使用[终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="531c7-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="531c7-189">下一节中的代码与前面的列表一应合。</span><span class="sxs-lookup"><span data-stu-id="531c7-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="531c7-190">有关测试代码的说明，请参阅[测试 CORS，](#testc)这些说明与前面的代码类似。</span><span class="sxs-lookup"><span data-stu-id="531c7-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="531c7-191">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-191">Disable CORS</span></span>

<span data-ttu-id="531c7-192">[[禁用 Cors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性***不会***禁用终结点[路由](#ecors)已启用的 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="531c7-193">以下代码定义 CORS 策略`"MyPolicy"`：</span><span class="sxs-lookup"><span data-stu-id="531c7-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="531c7-194">以下代码禁用操作的`GetValues2`CORS：</span><span class="sxs-lookup"><span data-stu-id="531c7-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="531c7-195">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="531c7-195">The preceding code:</span></span>

* <span data-ttu-id="531c7-196">不支持带[终结点路由](#ecors)的 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="531c7-197">不定义[默认的 CORS 策略](#dp)。</span><span class="sxs-lookup"><span data-stu-id="531c7-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="531c7-198">使用[[启用 Cors（"我的策略"）]](#attr) `"MyPolicy"`为控制器启用 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="531c7-199">禁用方法的`GetValues2`CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="531c7-200">有关测试上述代码的说明，请参阅[测试 CORS。](#testc)</span><span class="sxs-lookup"><span data-stu-id="531c7-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="531c7-201">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="531c7-201">CORS policy options</span></span>

<span data-ttu-id="531c7-202">本节介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="531c7-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="531c7-203">设置允许的原点</span><span class="sxs-lookup"><span data-stu-id="531c7-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="531c7-204">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="531c7-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="531c7-205">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="531c7-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="531c7-206">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="531c7-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="531c7-207">跨源请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="531c7-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="531c7-208">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="531c7-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="531c7-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在 中`Startup.ConfigureServices`调用 。</span><span class="sxs-lookup"><span data-stu-id="531c7-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="531c7-210">对于某些选项，首先阅读["CORS 的工作原理"](#how-cors)部分可能会有所帮助。</span><span class="sxs-lookup"><span data-stu-id="531c7-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="531c7-211">设置允许的原点</span><span class="sxs-lookup"><span data-stu-id="531c7-211">Set the allowed origins</span></span>

<span data-ttu-id="531c7-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;允许使用任何方案 （或`http``https`） 从所有来源请求 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="531c7-213">`AllowAnyOrigin`不安全，因为*任何网站*都可以向应用发出交叉源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="531c7-214">指定`AllowAnyOrigin``AllowCredentials`和 是不安全的配置，可能会导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="531c7-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="531c7-215">当应用同时配置这两种方法时，CORS 服务返回无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="531c7-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="531c7-216">`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="531c7-217">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="531c7-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;将<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>策略的属性设为一个函数，允许源在评估是否允许原点时匹配配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="531c7-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="531c7-219">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="531c7-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="531c7-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="531c7-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="531c7-221">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="531c7-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="531c7-222">影响印前请求和`Access-Control-Allow-Methods`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="531c7-223">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="531c7-224">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="531c7-224">Set the allowed request headers</span></span>

<span data-ttu-id="531c7-225">要允许在 CORS 请求中发送特定标头，请调用作者[请求标头](https://xhr.spec.whatwg.org/#request)，调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="531c7-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="531c7-226">要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="531c7-227">`AllowAnyHeader`影响预检请求和[访问控制请求标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)。</span><span class="sxs-lookup"><span data-stu-id="531c7-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="531c7-228">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="531c7-229">仅当中`Access-Control-Request-Headers`发送的标头与 中`WithHeaders`所述标头完全匹配时`WithHeaders`，才能与 指定的特定标头匹配 CORS 中间件策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="531c7-230">例如，请考虑配置如下的应用：</span><span class="sxs-lookup"><span data-stu-id="531c7-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="531c7-231">CORS 中间件拒绝具有以下请求标头的预检请求，因为`Content-Language`（[标题名称.Content语言](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)） 未在`WithHeaders`中列出：</span><span class="sxs-lookup"><span data-stu-id="531c7-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="531c7-232">该应用程序返回*200 OK*响应，但不会将 CORS 标头发送回。</span><span class="sxs-lookup"><span data-stu-id="531c7-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="531c7-233">因此，浏览器不会尝试跨源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="531c7-234">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="531c7-234">Set the exposed response headers</span></span>

<span data-ttu-id="531c7-235">默认情况下，浏览器不会向应用公开所有响应标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="531c7-236">有关详细信息，请参阅[W3C 跨源资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="531c7-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="531c7-237">默认情况下可用的响应标头是：</span><span class="sxs-lookup"><span data-stu-id="531c7-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="531c7-238">CORS 规范调用这些标头*为简单响应标头*。</span><span class="sxs-lookup"><span data-stu-id="531c7-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="531c7-239">要使其他标头可供应用使用，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="531c7-240">跨源请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="531c7-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="531c7-241">凭据需要在 CORS 请求中特殊处理。</span><span class="sxs-lookup"><span data-stu-id="531c7-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="531c7-242">默认情况下，浏览器不会发送具有跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="531c7-243">凭据包括 Cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="531c7-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="531c7-244">要使用跨源请求发送凭据，客户端必须设置为`XMLHttpRequest.withCredentials``true`。</span><span class="sxs-lookup"><span data-stu-id="531c7-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="531c7-245">直接`XMLHttpRequest`使用：</span><span class="sxs-lookup"><span data-stu-id="531c7-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="531c7-246">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="531c7-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="531c7-247">使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="531c7-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="531c7-248">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-248">The server must allow the credentials.</span></span> <span data-ttu-id="531c7-249">要允许跨源凭据，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="531c7-250">HTTP 响应包括一`Access-Control-Allow-Credentials`个标头，该标头告诉浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="531c7-251">如果浏览器发送凭据，但响应不包含有效的`Access-Control-Allow-Credentials`标头，则浏览器不会向应用公开响应，并且跨源请求将失败。</span><span class="sxs-lookup"><span data-stu-id="531c7-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="531c7-252">允许跨源凭据存在安全风险。</span><span class="sxs-lookup"><span data-stu-id="531c7-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="531c7-253">另一个域中的网站可以在用户不知情的情况下代表用户向应用发送登录用户的凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="531c7-254">CORS 规范还规定，如果标头存在，`"*"``Access-Control-Allow-Credentials`则将原点设置为（所有源）无效。</span><span class="sxs-lookup"><span data-stu-id="531c7-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="531c7-255">预检请求</span><span class="sxs-lookup"><span data-stu-id="531c7-255">Preflight requests</span></span>

<span data-ttu-id="531c7-256">对于某些 CORS 请求，浏览器在发出实际请求之前会发送其他[OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="531c7-257">此请求称为[预检请求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。</span><span class="sxs-lookup"><span data-stu-id="531c7-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="531c7-258">如果以下***所有条件都***为 true，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="531c7-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="531c7-259">请求方法是 GET、头或 POST。</span><span class="sxs-lookup"><span data-stu-id="531c7-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="531c7-260">应用不设置请求`Accept`标头以外的 、、、、`Accept-Language``Content-Language``Content-Type`或`Last-Event-ID`。</span><span class="sxs-lookup"><span data-stu-id="531c7-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="531c7-261">标头`Content-Type`（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="531c7-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="531c7-262">为客户端请求设置的请求标头规则适用于应用通过调用`setRequestHeader`对象设置的`XMLHttpRequest`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="531c7-263">CORS 规范调用这些标头[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="531c7-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="531c7-264">该规则不适用于浏览器可以设置的标头，例如`User-Agent`。 `Host` `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="531c7-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="531c7-265">下面是与本文档["测试"](#testc)部分中的 **[放置测试]** 按钮所做的预检请求类似的示例响应。</span><span class="sxs-lookup"><span data-stu-id="531c7-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="531c7-266">预检请求使用[HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="531c7-267">它可能包括以下标头：</span><span class="sxs-lookup"><span data-stu-id="531c7-267">It may include the following headers:</span></span>

* <span data-ttu-id="531c7-268">[访问控制-请求方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="531c7-269">[访问控制-请求-标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="531c7-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="531c7-270">如前所述，这不包括浏览器设置的标头，如`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="531c7-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="531c7-271">访问控制-允许方法</span><span class="sxs-lookup"><span data-stu-id="531c7-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="531c7-272">如果预检请求被拒绝，应用将返回响应`200 OK`，但不会设置 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="531c7-273">因此，浏览器不会尝试跨源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="531c7-274">有关被拒绝的印前请求的示例，请参阅本文档的["测试 CORS"](#testc)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="531c7-275">使用 F12 工具，控制台应用会显示类似于以下错误之一的错误，具体取决于浏览器：</span><span class="sxs-lookup"><span data-stu-id="531c7-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="531c7-276">Firefox： 跨源请求被阻止： 同一源策略不允许读取 中的`https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`远程资源。</span><span class="sxs-lookup"><span data-stu-id="531c7-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="531c7-277">（原因：CORS 请求未成功）。</span><span class="sxs-lookup"><span data-stu-id="531c7-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="531c7-278">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="531c7-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="531c7-279">基于铬：CORS 策略阻止了以'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5来自源'https://cors3.azurewebsites.net获取的访问：对印前请求的响应未通过访问控制检查：请求的资源上不存在"访问-控制-允许-原点"标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="531c7-280">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="531c7-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="531c7-281">要允许特定标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="531c7-282">要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="531c7-283">浏览器的设置`Access-Control-Request-Headers`方式不一致。</span><span class="sxs-lookup"><span data-stu-id="531c7-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="531c7-284">如果任一：</span><span class="sxs-lookup"><span data-stu-id="531c7-284">If either:</span></span>

* <span data-ttu-id="531c7-285">标头设置为`"*"`</span><span class="sxs-lookup"><span data-stu-id="531c7-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="531c7-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>调用： 至少`Accept`包括 ，`Content-Type`和`Origin`， 以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="531c7-287">自动预检请求代码</span><span class="sxs-lookup"><span data-stu-id="531c7-287">Automatic preflight request code</span></span>

<span data-ttu-id="531c7-288">应用 CORS 策略时，</span><span class="sxs-lookup"><span data-stu-id="531c7-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="531c7-289">通过 调用`app.UseCors``Startup.Configure`在全球范围内调用 。</span><span class="sxs-lookup"><span data-stu-id="531c7-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="531c7-290">使用`[EnableCors]`属性。</span><span class="sxs-lookup"><span data-stu-id="531c7-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="531c7-291">ASP.NET核心响应预检选项请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="531c7-292">使用`RequireCors`当前基于每个终结点启用 CORS***不支持***自动预检请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="531c7-293">本文档的["测试 CORS"](#testc)部分演示了此行为。</span><span class="sxs-lookup"><span data-stu-id="531c7-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="531c7-294">预检请求的 [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="531c7-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="531c7-295">当使用适当的策略启用 CORS 时，ASP.NET核心通常会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="531c7-296">在某些情况下，情况可能并非如此。</span><span class="sxs-lookup"><span data-stu-id="531c7-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="531c7-297">例如，将[CORS 与终结点路由一起使用](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="531c7-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="531c7-298">以下代码使用[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性为 OPTIONS 请求创建终结点：</span><span class="sxs-lookup"><span data-stu-id="531c7-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="531c7-299">有关测试上述代码的说明，请参阅[使用终结点路由和 [HttpOptions] 测试 CORS。](#tcer)</span><span class="sxs-lookup"><span data-stu-id="531c7-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="531c7-300">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="531c7-300">Set the preflight expiration time</span></span>

<span data-ttu-id="531c7-301">标头`Access-Control-Max-Age`指定对预检请求的响应可以缓存多长时间。</span><span class="sxs-lookup"><span data-stu-id="531c7-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="531c7-302">要设置此标头，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>调用 ：</span><span class="sxs-lookup"><span data-stu-id="531c7-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="531c7-303">CORS 的工作原理</span><span class="sxs-lookup"><span data-stu-id="531c7-303">How CORS works</span></span>

<span data-ttu-id="531c7-304">本节介绍在 HTTP 消息级别发生的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="531c7-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="531c7-305">CORS**不是**一个安全功能。</span><span class="sxs-lookup"><span data-stu-id="531c7-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="531c7-306">CORS 是一种 W3C 标准，允许服务器放松同源策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="531c7-307">例如，恶意参与者可能会对您的网站使用[跨站点脚本 （XSS），](xref:security/cross-site-scripting)并对其启用 CORS 的网站执行跨站点请求以窃取信息。</span><span class="sxs-lookup"><span data-stu-id="531c7-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="531c7-308">通过允许 CORS，API 并不更安全。</span><span class="sxs-lookup"><span data-stu-id="531c7-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="531c7-309">由客户端（浏览器）来强制实施 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="531c7-310">服务器执行请求并返回响应，返回错误的是客户端并阻止响应。</span><span class="sxs-lookup"><span data-stu-id="531c7-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="531c7-311">例如，以下任何工具将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="531c7-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="531c7-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="531c7-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="531c7-313">Postman</span><span class="sxs-lookup"><span data-stu-id="531c7-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="531c7-314">.NET httpClient</span><span class="sxs-lookup"><span data-stu-id="531c7-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="531c7-315">通过在地址栏中输入 URL 来访问 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="531c7-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="531c7-316">这是服务器允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求的一种方式，否则将禁止这样做。</span><span class="sxs-lookup"><span data-stu-id="531c7-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="531c7-317">没有 CORS 的浏览器无法执行跨源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="531c7-318">在 CORS 之前[，JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)曾用于规避此限制。</span><span class="sxs-lookup"><span data-stu-id="531c7-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="531c7-319">JSONP 不使用 XHR，它使用`<script>`标记来接收响应。</span><span class="sxs-lookup"><span data-stu-id="531c7-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="531c7-320">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="531c7-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="531c7-321">[CORS 规范](https://www.w3.org/TR/cors/)引入了几个支持跨源请求的新 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="531c7-322">如果浏览器支持 CORS，它将自动为跨源请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="531c7-323">启用 CORS 不需要自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="531c7-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="531c7-324">已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的[PUT 测试按钮](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="531c7-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="531c7-325">下面是从["值](https://cors3.azurewebsites.net/)"测试按钮到`https://cors1.azurewebsites.net/api/values`的跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="531c7-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="531c7-326">标头`Origin`：</span><span class="sxs-lookup"><span data-stu-id="531c7-326">The `Origin` header:</span></span>

* <span data-ttu-id="531c7-327">提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="531c7-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="531c7-328">是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="531c7-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="531c7-329">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="531c7-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="531c7-330">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="531c7-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="531c7-331">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="531c7-331">**Request headers**</span></span>

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

<span data-ttu-id="531c7-332">在`OPTIONS`请求中，服务器在响应中设置**响应**`Access-Control-Allow-Origin: {allowed origin}`标头标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="531c7-333">例如，已部署[的示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [，删除 [启用 Cors]](https://cors1.azurewebsites.net/test?number=2)按钮`OPTIONS`请求包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="531c7-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="531c7-334">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="531c7-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="531c7-335">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="531c7-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="531c7-336">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="531c7-336">**Request headers**</span></span>

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

<span data-ttu-id="531c7-337">在前面的**响应标头中**，服务器在响应中设置[访问控制允许源](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="531c7-338">此`https://cors1.azurewebsites.net`标头的值与请求中的`Origin`标头匹配。</span><span class="sxs-lookup"><span data-stu-id="531c7-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="531c7-339">如果<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>调用 ，`Access-Control-Allow-Origin: *`则返回 的通配符值。</span><span class="sxs-lookup"><span data-stu-id="531c7-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="531c7-340">`AllowAnyOrigin`允许任何来源。</span><span class="sxs-lookup"><span data-stu-id="531c7-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="531c7-341">如果响应不包括标头，`Access-Control-Allow-Origin`则跨源请求将失败。</span><span class="sxs-lookup"><span data-stu-id="531c7-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="531c7-342">具体来说，浏览器不允许请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="531c7-343">即使服务器返回成功的响应，浏览器也不会使响应对客户端应用可用。</span><span class="sxs-lookup"><span data-stu-id="531c7-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="531c7-344">显示选项请求</span><span class="sxs-lookup"><span data-stu-id="531c7-344">Display OPTIONS requests</span></span>

<span data-ttu-id="531c7-345">默认情况下，Chrome 和边缘浏览器不会在 F12 工具的网络选项卡上显示选项请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="531c7-346">要在这些浏览器中显示选项请求，</span><span class="sxs-lookup"><span data-stu-id="531c7-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="531c7-347">`chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="531c7-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="531c7-348">禁用标志。</span><span class="sxs-lookup"><span data-stu-id="531c7-348">disable the flag.</span></span>
* <span data-ttu-id="531c7-349">重新 启动。</span><span class="sxs-lookup"><span data-stu-id="531c7-349">restart.</span></span>

<span data-ttu-id="531c7-350">默认情况下，Firefox 会显示选项请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="531c7-351">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-351">CORS in IIS</span></span>

<span data-ttu-id="531c7-352">部署到 IIS 时，如果服务器未配置为允许匿名访问，则 CORS 必须在 Windows 身份验证之前运行。</span><span class="sxs-lookup"><span data-stu-id="531c7-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="531c7-353">为了支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。</span><span class="sxs-lookup"><span data-stu-id="531c7-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="531c7-354">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-354">Test CORS</span></span>

<span data-ttu-id="531c7-355">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)具有用于测试 CORS 的代码。</span><span class="sxs-lookup"><span data-stu-id="531c7-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="531c7-356">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="531c7-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="531c7-357">该示例是添加了 Razor 页面的 API 项目：</span><span class="sxs-lookup"><span data-stu-id="531c7-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="531c7-358">`WithOrigins("https://localhost:<port>");`应仅用于测试类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)的示例应用。</span><span class="sxs-lookup"><span data-stu-id="531c7-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="531c7-359">下面`ValuesController`提供了用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="531c7-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="531c7-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)由[Rick.Docs.sample.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 包提供，并显示路线信息。</span><span class="sxs-lookup"><span data-stu-id="531c7-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="531c7-361">使用以下方法之一测试前面的示例代码：</span><span class="sxs-lookup"><span data-stu-id="531c7-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="531c7-362">在 中使用 部署的示例[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)应用。</span><span class="sxs-lookup"><span data-stu-id="531c7-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="531c7-363">无需下载示例。</span><span class="sxs-lookup"><span data-stu-id="531c7-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="531c7-364">`dotnet run`使用`https://localhost:5001`的默认 URL 运行示例。</span><span class="sxs-lookup"><span data-stu-id="531c7-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="531c7-365">从 Visual Studio 运行示例，端口设置为 44398，`https://localhost:44398`用于 URL。</span><span class="sxs-lookup"><span data-stu-id="531c7-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="531c7-366">将浏览器与 F12 工具一起使用：</span><span class="sxs-lookup"><span data-stu-id="531c7-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="531c7-367">选择"**值"** 按钮并查看 **"网络"** 选项卡中的标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="531c7-368">选择**PUT 测试**按钮。</span><span class="sxs-lookup"><span data-stu-id="531c7-368">Select the **PUT test** button.</span></span> <span data-ttu-id="531c7-369">有关显示 OPTIONS 请求的说明，请参阅[显示选项请求](#options)。</span><span class="sxs-lookup"><span data-stu-id="531c7-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="531c7-370">**PUT 测试**创建两个请求，一个选项预检请求和 PUT 请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="531c7-371">选择按钮**`GetValues2 [DisableCors]`** 以触发失败的 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="531c7-372">如文档中所述，响应返回 200 成功，但未发出 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="531c7-373">选择 **"控制台"** 选项卡以查看 CORS 错误。</span><span class="sxs-lookup"><span data-stu-id="531c7-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="531c7-374">根据浏览器的不同，将显示类似于以下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="531c7-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="531c7-375">CORS 策略`'https://cors1.azurewebsites.net/api/values/GetValues2'`阻止了从`'https://cors3.azurewebsites.net'`源提取的访问：请求的资源上不存在"访问-控制-允许源"标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="531c7-376">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="531c7-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="531c7-377">支持 CORS 的终结点可以使用工具进行测试，例如[卷曲](https://curl.haxx.se/)[、Fiddler](https://www.telerik.com/fiddler)或[Postman。](https://www.getpostman.com/)</span><span class="sxs-lookup"><span data-stu-id="531c7-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="531c7-378">使用工具时，`Origin`标头指定的请求的来源必须不同于接收请求的主机。</span><span class="sxs-lookup"><span data-stu-id="531c7-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="531c7-379">如果请求不是基于标头的值*的跨源*： `Origin`</span><span class="sxs-lookup"><span data-stu-id="531c7-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="531c7-380">CORS 中间件无需处理请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="531c7-381">响应中未返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="531c7-382">以下命令用于`curl`发出带有信息的 OPTIONS 请求：</span><span class="sxs-lookup"><span data-stu-id="531c7-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="531c7-383">使用端点路由和 [httpOptions] 测试 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="531c7-384">使用`RequireCors`当前基于每个终结点启用 CORS***不支持***[自动预检请求](#apf)。</span><span class="sxs-lookup"><span data-stu-id="531c7-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="531c7-385">请考虑以下使用[终结点路由启用 CORS 的代码](#ecors)：</span><span class="sxs-lookup"><span data-stu-id="531c7-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="531c7-386">以下内容`TodoItems1Controller`提供了用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="531c7-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="531c7-387">从已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[测试页](https://cors1.azurewebsites.net/test?number=1)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="531c7-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="531c7-388">**删除 [启用 Cors]** 和**GET [启用Cors]** 按钮成功，因为`[EnableCors]`端点具有并响应预检请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="531c7-389">其他终结点失败。</span><span class="sxs-lookup"><span data-stu-id="531c7-389">The other endpoints fails.</span></span> <span data-ttu-id="531c7-390">**GET**按钮失败，因为[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)发送：</span><span class="sxs-lookup"><span data-stu-id="531c7-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="531c7-391">以下内容`TodoItems2Controller`提供了类似的终结点，但包括用于响应 OPTIONS 请求的显式代码：</span><span class="sxs-lookup"><span data-stu-id="531c7-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="531c7-392">从已部署示例的[测试页](https://cors1.azurewebsites.net/test?number=2)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="531c7-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="531c7-393">在 **"控制器**下拉列表"中，选择 **"预检**"，然后**设置控制器**。</span><span class="sxs-lookup"><span data-stu-id="531c7-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="531c7-394">对`TodoItems2Controller`终结点的所有 CORS 调用都成功。</span><span class="sxs-lookup"><span data-stu-id="531c7-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="531c7-395">其他资源</span><span class="sxs-lookup"><span data-stu-id="531c7-395">Additional resources</span></span>

* [<span data-ttu-id="531c7-396">跨源资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="531c7-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="531c7-397">开始使用 IIS CORS 模块</span><span class="sxs-lookup"><span data-stu-id="531c7-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="531c7-398">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="531c7-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="531c7-399">本文演示如何在ASP.NET核心应用中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="531c7-400">浏览器安全性可防止网页向与服务网页的域不同的域发出请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="531c7-401">此限制称为*同源策略*。</span><span class="sxs-lookup"><span data-stu-id="531c7-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="531c7-402">同源策略可防止恶意站点从其他站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="531c7-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="531c7-403">有时，您可能希望允许其他网站向你的应用发出交叉源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="531c7-404">有关详细信息，请参阅[Mozilla CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="531c7-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="531c7-405">[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：</span><span class="sxs-lookup"><span data-stu-id="531c7-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="531c7-406">是允许服务器放松同源策略的 W3C 标准。</span><span class="sxs-lookup"><span data-stu-id="531c7-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="531c7-407">**CORS 不是**安全功能，可放松安全性。</span><span class="sxs-lookup"><span data-stu-id="531c7-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="531c7-408">通过允许 CORS，API 并不更安全。</span><span class="sxs-lookup"><span data-stu-id="531c7-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="531c7-409">有关详细信息，请参阅[CORS 的工作原理](#how-cors)。</span><span class="sxs-lookup"><span data-stu-id="531c7-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="531c7-410">允许服务器显式允许某些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="531c7-411">比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全、更灵活。</span><span class="sxs-lookup"><span data-stu-id="531c7-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="531c7-412">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="531c7-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="531c7-413">同一源</span><span class="sxs-lookup"><span data-stu-id="531c7-413">Same origin</span></span>

<span data-ttu-id="531c7-414">如果两个 URL 具有相同的方案、主机和端口[（RFC 6454），](https://tools.ietf.org/html/rfc6454)则它们具有相同的源源。</span><span class="sxs-lookup"><span data-stu-id="531c7-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="531c7-415">这两个 URL 具有相同的来源：</span><span class="sxs-lookup"><span data-stu-id="531c7-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="531c7-416">这些 URL 与前两个 URL 具有不同的来源：</span><span class="sxs-lookup"><span data-stu-id="531c7-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="531c7-417">`https://example.net`&ndash;不同的域</span><span class="sxs-lookup"><span data-stu-id="531c7-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="531c7-418">`https://www.example.com/foo.html`&ndash;不同的子域</span><span class="sxs-lookup"><span data-stu-id="531c7-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="531c7-419">`http://example.com/foo.html`&ndash;不同的方案</span><span class="sxs-lookup"><span data-stu-id="531c7-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="531c7-420">`https://example.com:9000/foo.html`&ndash;不同的端口</span><span class="sxs-lookup"><span data-stu-id="531c7-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="531c7-421">在比较源时，Internet Explorer 不考虑端口。</span><span class="sxs-lookup"><span data-stu-id="531c7-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="531c7-422">带有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="531c7-423">CORS 中间件处理跨源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="531c7-424">以下代码支持具有指定来源的整个应用的 CORS：</span><span class="sxs-lookup"><span data-stu-id="531c7-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="531c7-425">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="531c7-425">The preceding code:</span></span>

* <span data-ttu-id="531c7-426">将策略名称设为"myAllow\_特定原点"。</span><span class="sxs-lookup"><span data-stu-id="531c7-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="531c7-427">策略名称是任意的。</span><span class="sxs-lookup"><span data-stu-id="531c7-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="531c7-428">调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法，该方法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="531c7-429">使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的调用。</span><span class="sxs-lookup"><span data-stu-id="531c7-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="531c7-430">lambda 取一<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>个对象。</span><span class="sxs-lookup"><span data-stu-id="531c7-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="531c7-431">[配置选项](#cors-policy-options)（如`WithOrigins`）在本文的后面部分介绍。</span><span class="sxs-lookup"><span data-stu-id="531c7-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="531c7-432">方法<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="531c7-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="531c7-433">有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。</span><span class="sxs-lookup"><span data-stu-id="531c7-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="531c7-434">该方法<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>可以链接方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="531c7-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="531c7-435">注意： URL**不得**包含尾随斜杠`/`（ 。</span><span class="sxs-lookup"><span data-stu-id="531c7-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="531c7-436">如果 URL 终止与`/`，则比较`false`返回，并且不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="531c7-437">以下代码通过 CORS 中间件将 CORS 策略应用于所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="531c7-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="531c7-438">注意：`UseCors`必须在 之前`UseMvc`调用 。</span><span class="sxs-lookup"><span data-stu-id="531c7-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="531c7-439">请参阅[在 Razor 页面、控制器和操作方法中启用 CORS，](#ecors)以便在页面/控制器/操作级别应用 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="531c7-440">有关测试代码的说明，请参阅[测试 CORS，](#test)这些说明与前面的代码类似。</span><span class="sxs-lookup"><span data-stu-id="531c7-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="531c7-441">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-441">Enable CORS with attributes</span></span>

<span data-ttu-id="531c7-442">[&lbrack;启用 Cors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="531c7-443">该`[EnableCors]`属性为选定的端点启用 CORS，而不是所有端点。</span><span class="sxs-lookup"><span data-stu-id="531c7-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="531c7-444">用于`[EnableCors]`指定默认策略和`[EnableCors("{Policy String}")]`指定策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="531c7-445">该`[EnableCors]`属性可应用于：</span><span class="sxs-lookup"><span data-stu-id="531c7-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="531c7-446">剃刀页面`PageModel`</span><span class="sxs-lookup"><span data-stu-id="531c7-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="531c7-447">控制器</span><span class="sxs-lookup"><span data-stu-id="531c7-447">Controller</span></span>
* <span data-ttu-id="531c7-448">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="531c7-448">Controller action method</span></span>

<span data-ttu-id="531c7-449">您可以使用`[EnableCors]`属性对控制器/页面模型/操作应用不同的策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="531c7-450">当该`[EnableCors]`属性应用于控制器/页面模型/操作方法，并在中间件中启用 CORS 时，将应用***这两个***策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="531c7-451">我们建议***不要***合并策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="531c7-452">使用属性`[EnableCors]`或中间件，\***而不是两者**。</span><span class="sxs-lookup"><span data-stu-id="531c7-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="531c7-453">使用`[EnableCors]`时 **，不要**定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="531c7-454">以下代码对每种方法应用不同的策略：</span><span class="sxs-lookup"><span data-stu-id="531c7-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="531c7-455">以下代码创建 CORS 默认策略和名为 的策略`"AnotherPolicy"`：</span><span class="sxs-lookup"><span data-stu-id="531c7-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="531c7-456">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-456">Disable CORS</span></span>

<span data-ttu-id="531c7-457">[&lbrack;禁用 Cors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性禁用控制器/页面模型/操作的 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="531c7-458">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="531c7-458">CORS policy options</span></span>

<span data-ttu-id="531c7-459">本节介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="531c7-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="531c7-460">设置允许的原点</span><span class="sxs-lookup"><span data-stu-id="531c7-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="531c7-461">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="531c7-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="531c7-462">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="531c7-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="531c7-463">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="531c7-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="531c7-464">跨源请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="531c7-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="531c7-465">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="531c7-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="531c7-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在 中`Startup.ConfigureServices`调用 。</span><span class="sxs-lookup"><span data-stu-id="531c7-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="531c7-467">对于某些选项，首先阅读["CORS 的工作原理"](#how-cors)部分可能会有所帮助。</span><span class="sxs-lookup"><span data-stu-id="531c7-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="531c7-468">设置允许的原点</span><span class="sxs-lookup"><span data-stu-id="531c7-468">Set the allowed origins</span></span>

<span data-ttu-id="531c7-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;允许使用任何方案 （或`http``https`） 从所有来源请求 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="531c7-470">`AllowAnyOrigin`不安全，因为*任何网站*都可以向应用发出交叉源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="531c7-471">指定`AllowAnyOrigin``AllowCredentials`和 是不安全的配置，可能会导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="531c7-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="531c7-472">对于安全应用，如果客户端必须授权自己访问服务器资源，请指定确切的来源列表。</span><span class="sxs-lookup"><span data-stu-id="531c7-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="531c7-473">`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="531c7-474">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="531c7-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;将<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>策略的属性设为一个函数，允许源在评估是否允许原点时匹配配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="531c7-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="531c7-476">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="531c7-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="531c7-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="531c7-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="531c7-478">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="531c7-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="531c7-479">影响印前请求和`Access-Control-Allow-Methods`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="531c7-480">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="531c7-481">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="531c7-481">Set the allowed request headers</span></span>

<span data-ttu-id="531c7-482">要允许在 CORS 请求中发送特定标头，请调用作者*请求标头*，调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="531c7-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="531c7-483">要允许所有作者请求标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="531c7-484">此设置会影响预检请求和`Access-Control-Request-Headers`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="531c7-485">有关详细信息，请参阅[预检请求](#preflight-requests)部分。</span><span class="sxs-lookup"><span data-stu-id="531c7-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="531c7-486">无论 CorsPolicy.header 中配置的值`Access-Control-Request-Headers`如何，CORS 中间件始终允许发送 中的四个标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="531c7-487">此标头列表包括：</span><span class="sxs-lookup"><span data-stu-id="531c7-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="531c7-488">例如，请考虑配置如下的应用：</span><span class="sxs-lookup"><span data-stu-id="531c7-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="531c7-489">CORS 中间件使用以下请求标头成功响应预检请求，因为`Content-Language`始终被列入白名单：</span><span class="sxs-lookup"><span data-stu-id="531c7-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="531c7-490">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="531c7-490">Set the exposed response headers</span></span>

<span data-ttu-id="531c7-491">默认情况下，浏览器不会向应用公开所有响应标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="531c7-492">有关详细信息，请参阅[W3C 跨源资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="531c7-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="531c7-493">默认情况下可用的响应标头是：</span><span class="sxs-lookup"><span data-stu-id="531c7-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="531c7-494">CORS 规范调用这些标头*为简单响应标头*。</span><span class="sxs-lookup"><span data-stu-id="531c7-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="531c7-495">要使其他标头可供应用使用，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="531c7-496">跨源请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="531c7-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="531c7-497">凭据需要在 CORS 请求中特殊处理。</span><span class="sxs-lookup"><span data-stu-id="531c7-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="531c7-498">默认情况下，浏览器不会发送具有跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="531c7-499">凭据包括 Cookie 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="531c7-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="531c7-500">要使用跨源请求发送凭据，客户端必须设置为`XMLHttpRequest.withCredentials``true`。</span><span class="sxs-lookup"><span data-stu-id="531c7-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="531c7-501">直接`XMLHttpRequest`使用：</span><span class="sxs-lookup"><span data-stu-id="531c7-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="531c7-502">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="531c7-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="531c7-503">使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="531c7-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="531c7-504">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-504">The server must allow the credentials.</span></span> <span data-ttu-id="531c7-505">要允许跨源凭据，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="531c7-506">HTTP 响应包括一`Access-Control-Allow-Credentials`个标头，该标头告诉浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="531c7-507">如果浏览器发送凭据，但响应不包含有效的`Access-Control-Allow-Credentials`标头，则浏览器不会向应用公开响应，并且跨源请求将失败。</span><span class="sxs-lookup"><span data-stu-id="531c7-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="531c7-508">允许跨源凭据存在安全风险。</span><span class="sxs-lookup"><span data-stu-id="531c7-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="531c7-509">另一个域中的网站可以在用户不知情的情况下代表用户向应用发送登录用户的凭据。</span><span class="sxs-lookup"><span data-stu-id="531c7-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="531c7-510">CORS 规范还规定，如果标头存在，`"*"``Access-Control-Allow-Credentials`则将原点设置为（所有源）无效。</span><span class="sxs-lookup"><span data-stu-id="531c7-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="531c7-511">预检请求</span><span class="sxs-lookup"><span data-stu-id="531c7-511">Preflight requests</span></span>

<span data-ttu-id="531c7-512">对于某些 CORS 请求，浏览器在发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="531c7-513">此请求称为*预检请求*。</span><span class="sxs-lookup"><span data-stu-id="531c7-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="531c7-514">如果以下条件为 true，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="531c7-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="531c7-515">请求方法是 GET、头或 POST。</span><span class="sxs-lookup"><span data-stu-id="531c7-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="531c7-516">应用不设置请求`Accept`标头以外的 、、、、`Accept-Language``Content-Language``Content-Type`或`Last-Event-ID`。</span><span class="sxs-lookup"><span data-stu-id="531c7-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="531c7-517">标头`Content-Type`（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="531c7-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="531c7-518">为客户端请求设置的请求标头规则适用于应用通过调用`setRequestHeader`对象设置的`XMLHttpRequest`标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="531c7-519">CORS 规范调用这些标头*作者请求标头*。</span><span class="sxs-lookup"><span data-stu-id="531c7-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="531c7-520">该规则不适用于浏览器可以设置的标头，例如`User-Agent`。 `Host` `Content-Length`</span><span class="sxs-lookup"><span data-stu-id="531c7-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="531c7-521">以下是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="531c7-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="531c7-522">飞行前请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="531c7-523">它包括两个特殊的标头：</span><span class="sxs-lookup"><span data-stu-id="531c7-523">It includes two special headers:</span></span>

* <span data-ttu-id="531c7-524">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="531c7-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="531c7-525">`Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="531c7-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="531c7-526">如前所述，这不包括浏览器设置的标头，如`User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="531c7-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="531c7-527">当使用适当的策略启用 CORS 时，ASP.NET核心通常会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="531c7-528">[有关预检请求，请参阅 [HttpOptions] 属性](#pro)。</span><span class="sxs-lookup"><span data-stu-id="531c7-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="531c7-529">CORS 预检请求可能包含一个`Access-Control-Request-Headers`标头，该标头向服务器指示与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="531c7-530">要允许特定标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="531c7-531">要允许所有作者请求标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：</span><span class="sxs-lookup"><span data-stu-id="531c7-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="531c7-532">浏览器的设置`Access-Control-Request-Headers`方式并不完全一致。</span><span class="sxs-lookup"><span data-stu-id="531c7-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="531c7-533">如果将标头设置为 （或使用`"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>） 以外的任何内容，则至少应包括`Accept`和`Content-Type` `Origin`，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="531c7-534">以下是对预检请求的示例响应（假设服务器允许该请求）：</span><span class="sxs-lookup"><span data-stu-id="531c7-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="531c7-535">响应包括列出`Access-Control-Allow-Methods`允许的方法的标头，并选择一个`Access-Control-Allow-Headers`标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="531c7-536">如果预检请求成功，浏览器将发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="531c7-537">如果预检请求被拒绝，应用将返回*200 OK*响应，但不会将 CORS 标头发送回。</span><span class="sxs-lookup"><span data-stu-id="531c7-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="531c7-538">因此，浏览器不会尝试跨源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="531c7-539">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="531c7-539">Set the preflight expiration time</span></span>

<span data-ttu-id="531c7-540">标头`Access-Control-Max-Age`指定对预检请求的响应可以缓存多长时间。</span><span class="sxs-lookup"><span data-stu-id="531c7-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="531c7-541">要设置此标头，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>调用 ：</span><span class="sxs-lookup"><span data-stu-id="531c7-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="531c7-542">CORS 的工作原理</span><span class="sxs-lookup"><span data-stu-id="531c7-542">How CORS works</span></span>

<span data-ttu-id="531c7-543">本节介绍在 HTTP 消息级别发生的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="531c7-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="531c7-544">CORS**不是**一个安全功能。</span><span class="sxs-lookup"><span data-stu-id="531c7-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="531c7-545">CORS 是一种 W3C 标准，允许服务器放松同源策略。</span><span class="sxs-lookup"><span data-stu-id="531c7-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="531c7-546">例如，恶意参与者可以使用[防止跨站点脚本 （XSS）](xref:security/cross-site-scripting)攻击您的网站，并对其启用 CORS 的网站执行跨站点请求以窃取信息。</span><span class="sxs-lookup"><span data-stu-id="531c7-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="531c7-547">通过允许 CORS，您的 API 并不更安全。</span><span class="sxs-lookup"><span data-stu-id="531c7-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="531c7-548">由客户端（浏览器）来强制实施 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="531c7-549">服务器执行请求并返回响应，返回错误的是客户端并阻止响应。</span><span class="sxs-lookup"><span data-stu-id="531c7-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="531c7-550">例如，以下任何工具将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="531c7-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="531c7-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="531c7-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="531c7-552">Postman</span><span class="sxs-lookup"><span data-stu-id="531c7-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="531c7-553">.NET httpClient</span><span class="sxs-lookup"><span data-stu-id="531c7-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="531c7-554">通过在地址栏中输入 URL 来访问 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="531c7-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="531c7-555">这是服务器允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求的一种方式，否则将禁止这样做。</span><span class="sxs-lookup"><span data-stu-id="531c7-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="531c7-556">浏览器（没有 CORS）不能执行交叉源请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="531c7-557">在 CORS 之前[，JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)曾用于规避此限制。</span><span class="sxs-lookup"><span data-stu-id="531c7-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="531c7-558">JSONP 不使用 XHR，它使用`<script>`标记来接收响应。</span><span class="sxs-lookup"><span data-stu-id="531c7-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="531c7-559">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="531c7-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="531c7-560">[CORS 规范](https://www.w3.org/TR/cors/)引入了几个支持跨源请求的新 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="531c7-561">如果浏览器支持 CORS，它将自动为跨源请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="531c7-562">启用 CORS 不需要自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="531c7-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="531c7-563">下面是跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="531c7-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="531c7-564">标头`Origin`提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="531c7-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="531c7-565">标头`Origin`是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="531c7-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="531c7-566">如果服务器允许请求，它将在`Access-Control-Allow-Origin`响应中设置标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="531c7-567">此标头的值与请求中的`Origin`标头匹配，或者是通配符值`"*"`，这意味着允许任何源：</span><span class="sxs-lookup"><span data-stu-id="531c7-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="531c7-568">如果响应不包括标头，`Access-Control-Allow-Origin`则跨源请求将失败。</span><span class="sxs-lookup"><span data-stu-id="531c7-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="531c7-569">具体来说，浏览器不允许请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="531c7-570">即使服务器返回成功的响应，浏览器也不会使响应对客户端应用可用。</span><span class="sxs-lookup"><span data-stu-id="531c7-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="531c7-571">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-571">Test CORS</span></span>

<span data-ttu-id="531c7-572">要测试 CORS：</span><span class="sxs-lookup"><span data-stu-id="531c7-572">To test CORS:</span></span>

1. <span data-ttu-id="531c7-573">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="531c7-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="531c7-574">或者，您可以[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="531c7-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="531c7-575">使用本文档中的一种方法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="531c7-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="531c7-576">例如：</span><span class="sxs-lookup"><span data-stu-id="531c7-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="531c7-577">`WithOrigins("https://localhost:<port>");`应仅用于测试类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)的示例应用。</span><span class="sxs-lookup"><span data-stu-id="531c7-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="531c7-578">创建 Web 应用项目（剃须页面或 MVC）。</span><span class="sxs-lookup"><span data-stu-id="531c7-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="531c7-579">该示例使用剃刀页。</span><span class="sxs-lookup"><span data-stu-id="531c7-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="531c7-580">您可以在与 API 项目相同的解决方案中创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="531c7-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="531c7-581">将以下突出显示的代码添加到*Index.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="531c7-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="531c7-582">在前面的代码中，替换为`url: 'https://<web app>.azurewebsites.net/api/values/1',`已部署应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="531c7-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="531c7-583">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="531c7-583">Deploy the API project.</span></span> <span data-ttu-id="531c7-584">例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="531c7-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="531c7-585">从桌面运行剃刀页面或 MVC 应用，然后单击 **"测试**"按钮。</span><span class="sxs-lookup"><span data-stu-id="531c7-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="531c7-586">使用 F12 工具查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="531c7-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="531c7-587">从`WithOrigins`中删除本地主机源并部署应用。</span><span class="sxs-lookup"><span data-stu-id="531c7-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="531c7-588">或者，使用其他端口运行客户端应用。</span><span class="sxs-lookup"><span data-stu-id="531c7-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="531c7-589">例如，从可视化工作室运行。</span><span class="sxs-lookup"><span data-stu-id="531c7-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="531c7-590">使用客户端应用进行测试。</span><span class="sxs-lookup"><span data-stu-id="531c7-590">Test with the client app.</span></span> <span data-ttu-id="531c7-591">CORS 失败返回错误，但错误消息对 JavaScript 不可用。</span><span class="sxs-lookup"><span data-stu-id="531c7-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="531c7-592">使用 F12 工具中的控制台选项卡查看错误。</span><span class="sxs-lookup"><span data-stu-id="531c7-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="531c7-593">根据浏览器的不同，您会收到类似于以下内容的错误（在 F12 工具控制台中）：</span><span class="sxs-lookup"><span data-stu-id="531c7-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="531c7-594">使用微软边缘：</span><span class="sxs-lookup"><span data-stu-id="531c7-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="531c7-595">**SEC7120： [CORS]`https://localhost:44375`在"访问`https://localhost:44375`-控制-允许-原点"响应标头中未找到跨源资源`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="531c7-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="531c7-596">使用铬：</span><span class="sxs-lookup"><span data-stu-id="531c7-596">Using Chrome:</span></span>

     <span data-ttu-id="531c7-597">**CORS 策略阻止了从`https://webapi.azurewebsites.net/api/values/1`源`https://localhost:44375`处对 XMLHttpRequest 的访问：请求的资源上不存在"访问-控制-允许源"标头。**</span><span class="sxs-lookup"><span data-stu-id="531c7-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="531c7-598">支持 CORS 的终结点可以使用工具进行测试，例如[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)。</span><span class="sxs-lookup"><span data-stu-id="531c7-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="531c7-599">使用工具时，`Origin`标头指定的请求的来源必须不同于接收请求的主机。</span><span class="sxs-lookup"><span data-stu-id="531c7-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="531c7-600">如果请求不是基于标头的值*的跨源*： `Origin`</span><span class="sxs-lookup"><span data-stu-id="531c7-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="531c7-601">CORS 中间件无需处理请求。</span><span class="sxs-lookup"><span data-stu-id="531c7-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="531c7-602">响应中未返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="531c7-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="531c7-603">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="531c7-603">CORS in IIS</span></span>

<span data-ttu-id="531c7-604">部署到 IIS 时，如果服务器未配置为允许匿名访问，则 CORS 必须在 Windows 身份验证之前运行。</span><span class="sxs-lookup"><span data-stu-id="531c7-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="531c7-605">为了支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。</span><span class="sxs-lookup"><span data-stu-id="531c7-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="531c7-606">其他资源</span><span class="sxs-lookup"><span data-stu-id="531c7-606">Additional resources</span></span>

* [<span data-ttu-id="531c7-607">跨源资源共享 (CORS)</span><span class="sxs-lookup"><span data-stu-id="531c7-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="531c7-608">开始使用 IIS CORS 模块</span><span class="sxs-lookup"><span data-stu-id="531c7-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
