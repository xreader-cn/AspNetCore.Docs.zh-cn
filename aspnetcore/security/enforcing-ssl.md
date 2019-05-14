---
title: 强制实施 HTTPS 在 ASP.NET Core
author: rick-anderson
description: 了解如何在 ASP.NET Core web 应用中需要 HTTPS/TLS。
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: e27e0c31b128cbd7d71bf7b83a2d33cc89ea3ab1
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610424"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="1710b-103">强制实施 HTTPS 在 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1710b-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="1710b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1710b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1710b-105">本文档演示如何：</span><span class="sxs-lookup"><span data-stu-id="1710b-105">This document shows how to:</span></span>

* <span data-ttu-id="1710b-106">要求所有请求使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="1710b-107">将所有 HTTP 请求重都定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="1710b-108">没有 API 可以防止客户端上的第一个请求发送敏感数据。</span><span class="sxs-lookup"><span data-stu-id="1710b-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

> [!WARNING]
> <span data-ttu-id="1710b-109">不要**不**使用[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute)接收敏感信息的 Web api。</span><span class="sxs-lookup"><span data-stu-id="1710b-109">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="1710b-110">`RequireHttpsAttribute` 使用 HTTP 状态代码将从 HTTP 到 HTTPS 的浏览器重定向。</span><span class="sxs-lookup"><span data-stu-id="1710b-110">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="1710b-111">API 客户端可能无法理解或遵循从 HTTP 到 HTTPS 的重定向。</span><span class="sxs-lookup"><span data-stu-id="1710b-111">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="1710b-112">此类客户端可能会通过 HTTP 发送的信息。</span><span class="sxs-lookup"><span data-stu-id="1710b-112">Such clients may send information over HTTP.</span></span> <span data-ttu-id="1710b-113">Web Api 应具有下列任一：</span><span class="sxs-lookup"><span data-stu-id="1710b-113">Web APIs should either:</span></span>
>
> * <span data-ttu-id="1710b-114">不侦听 HTTP。</span><span class="sxs-lookup"><span data-stu-id="1710b-114">Not listen on HTTP.</span></span>
> * <span data-ttu-id="1710b-115">关闭与状态代码 400 （错误请求） 的连接并不为请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1710b-115">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

## <a name="require-https"></a><span data-ttu-id="1710b-116">要求使用 HTTPS</span><span class="sxs-lookup"><span data-stu-id="1710b-116">Require HTTPS</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1710b-117">我们建议该生产 ASP.NET Core web 应用调用：</span><span class="sxs-lookup"><span data-stu-id="1710b-117">We recommend that production ASP.NET Core web apps call:</span></span>

* <span data-ttu-id="1710b-118">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-118">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="1710b-119">HSTS 中间件 ([UseHsts](#http-strict-transport-security-protocol-hsts)) 发送给客户端的 HTTP 严格传输安全性协议 (HSTS) 标头。</span><span class="sxs-lookup"><span data-stu-id="1710b-119">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="1710b-120">反向代理配置中部署的应用程序允许代理在处理连接安全 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="1710b-120">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="1710b-121">如果代理还处理 HTTPS 的重定向，则无需使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="1710b-121">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1710b-122">如果代理服务器还处理编写 HSTS 标头 (例如，[本机 HSTS 支持 IIS 10.0 (1709) 或更高版本](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support))，HSTS 中间件不所需的应用程序。</span><span class="sxs-lookup"><span data-stu-id="1710b-122">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="1710b-123">有关详细信息，请参阅[退出的 HTTPS/HSTS 在项目创建](#opt-out-of-httpshsts-on-project-creation)。</span><span class="sxs-lookup"><span data-stu-id="1710b-123">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="1710b-124">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="1710b-124">UseHttpsRedirection</span></span>

<span data-ttu-id="1710b-125">下面的代码调用`UseHttpsRedirection`在`Startup`类：</span><span class="sxs-lookup"><span data-stu-id="1710b-125">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="1710b-126">前面突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="1710b-126">The preceding highlighted code:</span></span>

* <span data-ttu-id="1710b-127">使用默认[HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect))。</span><span class="sxs-lookup"><span data-stu-id="1710b-127">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="1710b-128">使用默认[HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport)除非被重写 (null)`ASPNETCORE_HTTPS_PORT`环境变量或[IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)。</span><span class="sxs-lookup"><span data-stu-id="1710b-128">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="1710b-129">我们建议使用临时重定向而不是永久重定向。</span><span class="sxs-lookup"><span data-stu-id="1710b-129">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="1710b-130">链接缓存可以在开发环境中导致不稳定行为。</span><span class="sxs-lookup"><span data-stu-id="1710b-130">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="1710b-131">如果想要发送的永久重定向状态代码时该应用位于非开发环境，请参阅[配置在生产环境中的永久重定向](#configure-permanent-redirects-in-production)部分。</span><span class="sxs-lookup"><span data-stu-id="1710b-131">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="1710b-132">我们建议使用[HSTS](#http-strict-transport-security-protocol-hsts)将信号发送到仅保护资源的客户端请求应发送到应用 （仅在生产中）。</span><span class="sxs-lookup"><span data-stu-id="1710b-132">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="1710b-133">端口配置</span><span class="sxs-lookup"><span data-stu-id="1710b-133">Port configuration</span></span>

<span data-ttu-id="1710b-134">端口必须是可用于中间件将不安全的请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-134">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="1710b-135">如果没有端口不可用：</span><span class="sxs-lookup"><span data-stu-id="1710b-135">If no port is available:</span></span>

* <span data-ttu-id="1710b-136">不会发生重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-136">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="1710b-137">中间件将记录警告"无法确定重定向的 https 端口。"</span><span class="sxs-lookup"><span data-stu-id="1710b-137">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="1710b-138">指定使用任何以下方法之一的 HTTPS 端口：</span><span class="sxs-lookup"><span data-stu-id="1710b-138">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="1710b-139">设置[HttpsRedirectionOptions.HttpsPort](#options)。</span><span class="sxs-lookup"><span data-stu-id="1710b-139">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>
* <span data-ttu-id="1710b-140">设置`ASPNETCORE_HTTPS_PORT`环境变量或[https_port Web 主机配置设置](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="1710b-140">Set the `ASPNETCORE_HTTPS_PORT` environment variable or [https_port Web Host configuration setting](xref:fundamentals/host/web-host#https-port):</span></span>

  <span data-ttu-id="1710b-141">**密钥**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="1710b-141">**Key**: `https_port`</span></span>  
  <span data-ttu-id="1710b-142">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="1710b-142">**Type**: *string*</span></span>  
  <span data-ttu-id="1710b-143">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="1710b-143">**Default**: A default value isn't set.</span></span>  
  <span data-ttu-id="1710b-144">**设置使用**：`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="1710b-144">**Set using**: `UseSetting`</span></span>  
  <span data-ttu-id="1710b-145">**环境变量**:`<PREFIX_>HTTPS_PORT` (该前缀`ASPNETCORE_`使用时[Web 主机](xref:fundamentals/host/web-host)。)</span><span class="sxs-lookup"><span data-stu-id="1710b-145">**Environment variable**: `<PREFIX_>HTTPS_PORT` (The prefix is `ASPNETCORE_` when using the [Web Host](xref:fundamentals/host/web-host).)</span></span>

  <span data-ttu-id="1710b-146">配置时<xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>在`Program`:</span><span class="sxs-lookup"><span data-stu-id="1710b-146">When configuring an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span></span>

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* <span data-ttu-id="1710b-147">指示具有安全方案使用的端口`ASPNETCORE_URLS`环境变量。</span><span class="sxs-lookup"><span data-stu-id="1710b-147">Indicate a port with the secure scheme using the `ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="1710b-148">将服务器配置为环境变量。</span><span class="sxs-lookup"><span data-stu-id="1710b-148">The environment variable configures the server.</span></span> <span data-ttu-id="1710b-149">中间件间接发现通过 HTTPS 端口<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>。</span><span class="sxs-lookup"><span data-stu-id="1710b-149">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="1710b-150">反向代理部署这种方法不起作用。</span><span class="sxs-lookup"><span data-stu-id="1710b-150">This approach doesn't work in reverse proxy deployments.</span></span>
* <span data-ttu-id="1710b-151">在开发中，在中设置 HTTPS URL *launchsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="1710b-151">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="1710b-152">使用 IIS Express 时，请启用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-152">Enable HTTPS when IIS Express is used.</span></span>
* <span data-ttu-id="1710b-153">配置 HTTPS URL 终结点的面向公众 edge 部署[Kestrel](xref:fundamentals/servers/kestrel)服务器或[HTTP.sys](xref:fundamentals/servers/httpsys)服务器。</span><span class="sxs-lookup"><span data-stu-id="1710b-153">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="1710b-154">仅**一个 HTTPS 端口**应用使用。</span><span class="sxs-lookup"><span data-stu-id="1710b-154">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="1710b-155">中间件发现通过端口<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>。</span><span class="sxs-lookup"><span data-stu-id="1710b-155">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="1710b-156">当应用在反向代理配置中，运行时<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>不可用。</span><span class="sxs-lookup"><span data-stu-id="1710b-156">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="1710b-157">设置使用一种其他方法在本部分中所述的端口。</span><span class="sxs-lookup"><span data-stu-id="1710b-157">Set the port using one of the other approaches described in this section.</span></span>

<span data-ttu-id="1710b-158">当 Kestrel 或 HTTP.sys 用作面向公众的边缘服务器时，必须配置 Kestrel 或 HTTP.sys，若要在其上同时侦听：</span><span class="sxs-lookup"><span data-stu-id="1710b-158">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="1710b-159">其中重定向客户端的安全端口 (通常情况下，在生产和开发中的为 5001 的 443)。</span><span class="sxs-lookup"><span data-stu-id="1710b-159">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="1710b-160">不安全端口 (通常情况下，在生产环境中为 80) 和 5000 开发中。</span><span class="sxs-lookup"><span data-stu-id="1710b-160">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="1710b-161">不安全的端口必须可由客户端按顺序应用接收不安全的请求，并将客户端重定向到安全的端口。</span><span class="sxs-lookup"><span data-stu-id="1710b-161">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="1710b-162">有关详细信息，请参阅[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)或<xref:fundamentals/servers/httpsys>。</span><span class="sxs-lookup"><span data-stu-id="1710b-162">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="1710b-163">部署方案</span><span class="sxs-lookup"><span data-stu-id="1710b-163">Deployment scenarios</span></span>

<span data-ttu-id="1710b-164">客户端和服务器之间的所有防火墙也都必须打开的流量的通信端口。</span><span class="sxs-lookup"><span data-stu-id="1710b-164">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="1710b-165">如果反向代理配置中将请求转发，则使用[转发头中间件](xref:host-and-deploy/proxy-load-balancer)调用前 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="1710b-165">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1710b-166">转发头中间件更新`Request.Scheme`，并使用`X-Forwarded-Proto`标头。</span><span class="sxs-lookup"><span data-stu-id="1710b-166">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="1710b-167">中间件允许重定向 Uri 和其他安全策略才能正常工作。</span><span class="sxs-lookup"><span data-stu-id="1710b-167">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="1710b-168">转发头中间件不使用时后, 端应用程序可能不接收正确的方案和最终会在重定向循环。</span><span class="sxs-lookup"><span data-stu-id="1710b-168">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="1710b-169">常见的最终用户错误消息是已发生过多的重定向。</span><span class="sxs-lookup"><span data-stu-id="1710b-169">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="1710b-170">部署到 Azure 应用服务时，请按照中的指导[教程：将现有自定义 SSL 证书绑定到 Azure Web 应用](/azure/app-service/app-service-web-tutorial-custom-ssl)。</span><span class="sxs-lookup"><span data-stu-id="1710b-170">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="1710b-171">选项</span><span class="sxs-lookup"><span data-stu-id="1710b-171">Options</span></span>

<span data-ttu-id="1710b-172">以下突出显示的代码调用[AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection)配置中间件选项：</span><span class="sxs-lookup"><span data-stu-id="1710b-172">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

<span data-ttu-id="1710b-173">调用`AddHttpsRedirection`只是用来更改的值`HttpsPort`或`RedirectStatusCode`。</span><span class="sxs-lookup"><span data-stu-id="1710b-173">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="1710b-174">前面突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="1710b-174">The preceding highlighted code:</span></span>

* <span data-ttu-id="1710b-175">集[HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*)到<xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="1710b-175">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="1710b-176">使用的字段<xref:Microsoft.AspNetCore.Http.StatusCodes>分配到的类`RedirectStatusCode`。</span><span class="sxs-lookup"><span data-stu-id="1710b-176">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="1710b-177">设置 HTTPS 端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="1710b-177">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="1710b-178">默认值为 443。</span><span class="sxs-lookup"><span data-stu-id="1710b-178">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="1710b-179">在生产环境中配置永久重定向</span><span class="sxs-lookup"><span data-stu-id="1710b-179">Configure permanent redirects in production</span></span>

<span data-ttu-id="1710b-180">中间件默认为发送[Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)与所有重定向。</span><span class="sxs-lookup"><span data-stu-id="1710b-180">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="1710b-181">如果想要发送的永久重定向状态代码，非开发环境中应用程序时，包装在对非开发环境的条件检查中的中间件的选项配置。</span><span class="sxs-lookup"><span data-stu-id="1710b-181">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

<span data-ttu-id="1710b-182">配置时`IWebHostBuilder`中*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="1710b-182">When configuring an `IWebHostBuilder` in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="1710b-183">HTTPS 重定向中间件另一种方法</span><span class="sxs-lookup"><span data-stu-id="1710b-183">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="1710b-184">使用 HTTPS 重定向中间件的替代方法 (`UseHttpsRedirection`) 是使用 URL 重写中间件 (`AddRedirectToHttps`)。</span><span class="sxs-lookup"><span data-stu-id="1710b-184">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="1710b-185">`AddRedirectToHttps` 此外可以设置的状态代码和端口时执行重定向。</span><span class="sxs-lookup"><span data-stu-id="1710b-185">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="1710b-186">有关详细信息，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。</span><span class="sxs-lookup"><span data-stu-id="1710b-186">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="1710b-187">当将重定向到 HTTPS 而无需其他重定向规则，我们建议使用 HTTPS 重定向中间件 (`UseHttpsRedirection`) 本主题中所述。</span><span class="sxs-lookup"><span data-stu-id="1710b-187">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="1710b-188">[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute)用于要求 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="1710b-188">The [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require HTTPS.</span></span> <span data-ttu-id="1710b-189">`[RequireHttpsAttribute]` 可以修饰控制器或方法，也可以全局应用。</span><span class="sxs-lookup"><span data-stu-id="1710b-189">`[RequireHttpsAttribute]` can decorate controllers or methods, or can be applied globally.</span></span> <span data-ttu-id="1710b-190">若要全局应用该属性，将以下代码添加到`ConfigureServices`在`Startup`:</span><span class="sxs-lookup"><span data-stu-id="1710b-190">To apply the attribute globally, add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

<span data-ttu-id="1710b-191">前面突出显示的代码要求所有请求都使用`HTTPS`; 因此，HTTP 请求将被忽略。</span><span class="sxs-lookup"><span data-stu-id="1710b-191">The preceding highlighted code requires all requests use `HTTPS`; therefore, HTTP requests are ignored.</span></span> <span data-ttu-id="1710b-192">以下突出显示的代码将所有 HTTP 请求重都定向到 HTTPS:</span><span class="sxs-lookup"><span data-stu-id="1710b-192">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

<span data-ttu-id="1710b-193">有关详细信息，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。</span><span class="sxs-lookup"><span data-stu-id="1710b-193">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="1710b-194">中间件还允许应用执行重定向时设置的状态代码或状态代码和端口。</span><span class="sxs-lookup"><span data-stu-id="1710b-194">The middleware also permits the app to set the status code or the status code and the port when the redirect is executed.</span></span>

<span data-ttu-id="1710b-195">全局需要 HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) 是最佳安全方案。</span><span class="sxs-lookup"><span data-stu-id="1710b-195">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="1710b-196">将应用`[RequireHttps]`属性设置为所有控制器/Razor 页面不会被视为与需要 HTTPS 全局一样安全。</span><span class="sxs-lookup"><span data-stu-id="1710b-196">Applying the `[RequireHttps]` attribute to all controllers/Razor Pages isn't considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="1710b-197">不能保证`[RequireHttps]`时添加新的控制器和 Razor 页面应用属性。</span><span class="sxs-lookup"><span data-stu-id="1710b-197">You can't guarantee the `[RequireHttps]` attribute is applied when new controllers and Razor Pages are added.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="1710b-198">HTTP 严格传输安全协议 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="1710b-198">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="1710b-199">每个[OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)， [HTTP 严格传输安全性 (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet)是通过响应标头使用的 web 应用指定选择的安全增强功能。</span><span class="sxs-lookup"><span data-stu-id="1710b-199">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="1710b-200">当[浏览器支持 HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)收到此标头：</span><span class="sxs-lookup"><span data-stu-id="1710b-200">When a [browser that supports HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) receives this header:</span></span>

* <span data-ttu-id="1710b-201">在浏览器将会阻止通过 HTTP 发送的任何通信的域的配置存储。</span><span class="sxs-lookup"><span data-stu-id="1710b-201">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="1710b-202">在浏览器强制通过 HTTPS 进行的所有通信。</span><span class="sxs-lookup"><span data-stu-id="1710b-202">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="1710b-203">在浏览器可防止用户使用不受信任或无效的证书。</span><span class="sxs-lookup"><span data-stu-id="1710b-203">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="1710b-204">在浏览器禁用允许用户暂时信任此证书的提示。</span><span class="sxs-lookup"><span data-stu-id="1710b-204">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="1710b-205">因为由客户端强制执行 HSTS 它具有一些限制：</span><span class="sxs-lookup"><span data-stu-id="1710b-205">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="1710b-206">客户端必须支持 HSTS。</span><span class="sxs-lookup"><span data-stu-id="1710b-206">The client must support HSTS.</span></span>
* <span data-ttu-id="1710b-207">HSTS 要求至少一个成功的 HTTPS 请求能够建立 HSTS 策略。</span><span class="sxs-lookup"><span data-stu-id="1710b-207">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="1710b-208">应用程序必须检查每个 HTTP 请求和重定向或拒绝的 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="1710b-208">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="1710b-209">ASP.NET Core 2.1 或更高版本实现与 HSTS`UseHsts`扩展方法。</span><span class="sxs-lookup"><span data-stu-id="1710b-209">ASP.NET Core 2.1 or later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="1710b-210">下面的代码调用`UseHsts`时应用不在[开发模式](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="1710b-210">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

<span data-ttu-id="1710b-211">`UseHsts` 不建议在开发过程中由于 HSTS 设置高度可缓存的浏览器。</span><span class="sxs-lookup"><span data-stu-id="1710b-211">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="1710b-212">默认情况下，`UseHsts`排除本地环回地址。</span><span class="sxs-lookup"><span data-stu-id="1710b-212">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="1710b-213">对于生产环境首次实现 HTTPS 设置初始[HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*)为使用其中一个较小值<xref:System.TimeSpan>方法。</span><span class="sxs-lookup"><span data-stu-id="1710b-213">For production environments implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="1710b-214">设置的值从小时数不超过一天的以防到时需要还原 HTTP 到 HTTPS 基础结构。</span><span class="sxs-lookup"><span data-stu-id="1710b-214">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="1710b-215">确信 HTTPS 配置的可持续发展中后，增加 HSTS 最大期限值;常用的值为一年。</span><span class="sxs-lookup"><span data-stu-id="1710b-215">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="1710b-216">下面的代码：</span><span class="sxs-lookup"><span data-stu-id="1710b-216">The following code:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* <span data-ttu-id="1710b-217">设置预加载严格传输安全性标头的参数。</span><span class="sxs-lookup"><span data-stu-id="1710b-217">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="1710b-218">预加载不属于[RFC HSTS 规范](https://tools.ietf.org/html/rfc6797)，但要预加载 HSTS 站点上执行全新安装的 web 浏览器支持。</span><span class="sxs-lookup"><span data-stu-id="1710b-218">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="1710b-219">有关详细信息，请参阅 [https://hstspreload.org/](https://hstspreload.org/)。</span><span class="sxs-lookup"><span data-stu-id="1710b-219">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="1710b-220">使[includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)，这将 HSTS 策略应用到主机的子域。</span><span class="sxs-lookup"><span data-stu-id="1710b-220">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="1710b-221">显式将严格传输安全性标头的最大期限参数设置为 60 天。</span><span class="sxs-lookup"><span data-stu-id="1710b-221">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="1710b-222">如果未设置，默认值为 30 天。</span><span class="sxs-lookup"><span data-stu-id="1710b-222">If not set, defaults to 30 days.</span></span> <span data-ttu-id="1710b-223">请参阅[最大期限指令](https://tools.ietf.org/html/rfc6797#section-6.1.1)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="1710b-223">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="1710b-224">添加`example.com`到主机以排除列表。</span><span class="sxs-lookup"><span data-stu-id="1710b-224">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="1710b-225">`UseHsts` 不包括以下环回主机：</span><span class="sxs-lookup"><span data-stu-id="1710b-225">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="1710b-226">`localhost`：IPv4 环回地址。</span><span class="sxs-lookup"><span data-stu-id="1710b-226">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="1710b-227">`127.0.0.1`：IPv4 环回地址。</span><span class="sxs-lookup"><span data-stu-id="1710b-227">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="1710b-228">`[::1]`：IPv6 环回地址。</span><span class="sxs-lookup"><span data-stu-id="1710b-228">`[::1]` : The IPv6 loopback address.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="1710b-229">选择退出的 HTTPS/HSTS 在项目创建</span><span class="sxs-lookup"><span data-stu-id="1710b-229">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="1710b-230">在其中连接安全处理面向公众边缘网络的某些后端服务情况下，无需在每个节点上配置连接安全。</span><span class="sxs-lookup"><span data-stu-id="1710b-230">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="1710b-231">Web 应用从 Visual Studio 中或从模板生成[dotnet 新](/dotnet/core/tools/dotnet-new)命令启用[HTTPS 的重定向](#require-https)并[HSTS](#http-strict-transport-security-protocol-hsts)。</span><span class="sxs-lookup"><span data-stu-id="1710b-231">Web apps generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="1710b-232">对于不需要这些方案的部署，你可以选择退出的 HTTPS/HSTS 时从模板创建的应用。</span><span class="sxs-lookup"><span data-stu-id="1710b-232">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="1710b-233">若要选择退出的 HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="1710b-233">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1710b-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1710b-234">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="1710b-235">取消选中**配置为支持 HTTPS**复选框。</span><span class="sxs-lookup"><span data-stu-id="1710b-235">Uncheck the **Configure for HTTPS** check box.</span></span>

![新 ASP.NET Core Web 应用程序对话框中显示为 HTTPS 复选框未选定的配置。](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1710b-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1710b-237">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="1710b-238">使用 `--no-https` 选项。</span><span class="sxs-lookup"><span data-stu-id="1710b-238">Use the `--no-https` option.</span></span> <span data-ttu-id="1710b-239">例如</span><span class="sxs-lookup"><span data-stu-id="1710b-239">For example</span></span>

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="1710b-240">信任在 Windows 和 macOS 上的 ASP.NET Core HTTPS 开发证书</span><span class="sxs-lookup"><span data-stu-id="1710b-240">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="1710b-241">.NET core SDK 包括 HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="1710b-241">.NET Core SDK includes a HTTPS development certificate.</span></span> <span data-ttu-id="1710b-242">首次运行体验的一部分安装证书。</span><span class="sxs-lookup"><span data-stu-id="1710b-242">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="1710b-243">例如，`dotnet --info`生成类似于以下输出：</span><span class="sxs-lookup"><span data-stu-id="1710b-243">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="1710b-244">安装 .NET Core SDK 会将 ASP.NET Core HTTPS 开发证书安装到本地用户证书存储。</span><span class="sxs-lookup"><span data-stu-id="1710b-244">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="1710b-245">已安装证书，但它具有不受信任。</span><span class="sxs-lookup"><span data-stu-id="1710b-245">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="1710b-246">若要信任该证书执行一次性步骤，运行 dotnet`dev-certs`工具：</span><span class="sxs-lookup"><span data-stu-id="1710b-246">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="1710b-247">下面的命令提供有关 `dev-certs` 工具的帮助：</span><span class="sxs-lookup"><span data-stu-id="1710b-247">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="1710b-248">如何设置适用于 Docker 的开发人员证书</span><span class="sxs-lookup"><span data-stu-id="1710b-248">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="1710b-249">请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/6199)。</span><span class="sxs-lookup"><span data-stu-id="1710b-249">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

::: moniker-end

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="1710b-250">信任适用于 Linux 的 Windows 子系统中的 HTTPS 证书</span><span class="sxs-lookup"><span data-stu-id="1710b-250">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="1710b-251">Windows 子系统用于 Linux (WSL) 生成 HTTPS 自签名的证书。若要配置要信任 WSL 证书的 Windows 证书存储：</span><span class="sxs-lookup"><span data-stu-id="1710b-251">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="1710b-252">运行以下命令以导出 WSL 生成证书： `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="1710b-252">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="1710b-253">在 WSL 窗口中，运行以下命令： `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="1710b-253">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="1710b-254">上述命令设置环境变量，因此 Linux 使用 Windows 受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="1710b-254">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="1710b-255">其他信息</span><span class="sxs-lookup"><span data-stu-id="1710b-255">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="1710b-256">使用 Apache 在 Linux 上托管 ASP.NET Core:HTTPS 配置</span><span class="sxs-lookup"><span data-stu-id="1710b-256">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="1710b-257">使用 Nginx 在 Linux 上托管 ASP.NET Core:HTTPS 配置</span><span class="sxs-lookup"><span data-stu-id="1710b-257">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="1710b-258">如何在 IIS 上设置 SSL</span><span class="sxs-lookup"><span data-stu-id="1710b-258">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="1710b-259">OWASP HSTS 浏览器支持</span><span class="sxs-lookup"><span data-stu-id="1710b-259">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
