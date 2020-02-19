---
title: 强制实施 HTTPS 在 ASP.NET Core
author: rick-anderson
description: 了解如何在 ASP.NET Core web 应用中要求使用 HTTPS/TLS。
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 43f3abfa4bc311ed246f6f2585d522661e492039
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447147"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="ece97-103">强制实施 HTTPS 在 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ece97-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="ece97-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ece97-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ece97-105">本文档介绍如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ece97-105">This document shows how to:</span></span>

* <span data-ttu-id="ece97-106">所有请求都需要 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="ece97-107">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="ece97-108">任何 API 都不能阻止客户端发送第一个请求上的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="ece97-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="ece97-109">API 项目</span><span class="sxs-lookup"><span data-stu-id="ece97-109">API projects</span></span>
>
> <span data-ttu-id="ece97-110">不要**对**接收敏感信息的 Web Api 使用[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 。</span><span class="sxs-lookup"><span data-stu-id="ece97-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="ece97-111">`RequireHttpsAttribute` 使用 HTTP 状态代码将浏览器从 HTTP 重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="ece97-112">API 客户端可能不理解或遵循从 HTTP 到 HTTPS 的重定向。</span><span class="sxs-lookup"><span data-stu-id="ece97-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="ece97-113">此类客户端可以通过 HTTP 发送信息。</span><span class="sxs-lookup"><span data-stu-id="ece97-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="ece97-114">Web Api 应：</span><span class="sxs-lookup"><span data-stu-id="ece97-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="ece97-115">不侦听 HTTP。</span><span class="sxs-lookup"><span data-stu-id="ece97-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="ece97-116">关闭状态代码为400（错误请求）的连接，并且不为请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="ece97-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="ece97-117">HSTS 和 API 项目</span><span class="sxs-lookup"><span data-stu-id="ece97-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="ece97-118">默认 API 项目不包括[HSTS](#hsts) ，因为 HSTS 通常是仅限浏览器的指令。</span><span class="sxs-lookup"><span data-stu-id="ece97-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="ece97-119">其他调用方（如电话或桌面应用程序）**不**遵守说明。</span><span class="sxs-lookup"><span data-stu-id="ece97-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="ece97-120">即使是在浏览器中，通过 HTTP 对 API 进行单个身份验证调用也会对不安全网络产生风险。</span><span class="sxs-lookup"><span data-stu-id="ece97-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="ece97-121">安全方法是将 API 项目配置为仅侦听并通过 HTTPS 进行响应。</span><span class="sxs-lookup"><span data-stu-id="ece97-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="ece97-122">API 项目</span><span class="sxs-lookup"><span data-stu-id="ece97-122">API projects</span></span>
>
> <span data-ttu-id="ece97-123">不要**对**接收敏感信息的 Web Api 使用[RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) 。</span><span class="sxs-lookup"><span data-stu-id="ece97-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="ece97-124">`RequireHttpsAttribute` 使用 HTTP 状态代码将浏览器从 HTTP 重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="ece97-125">API 客户端可能不理解或遵循从 HTTP 到 HTTPS 的重定向。</span><span class="sxs-lookup"><span data-stu-id="ece97-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="ece97-126">此类客户端可以通过 HTTP 发送信息。</span><span class="sxs-lookup"><span data-stu-id="ece97-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="ece97-127">Web Api 应：</span><span class="sxs-lookup"><span data-stu-id="ece97-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="ece97-128">不侦听 HTTP。</span><span class="sxs-lookup"><span data-stu-id="ece97-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="ece97-129">关闭状态代码为400（错误请求）的连接，并且不为请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="ece97-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="ece97-130">要求使用 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ece97-130">Require HTTPS</span></span>

<span data-ttu-id="ece97-131">建议将生产 ASP.NET Core web 应用使用：</span><span class="sxs-lookup"><span data-stu-id="ece97-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="ece97-132">HTTPS 重定向中间件（<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>），用于将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="ece97-133">HSTS 中间件（[UseHsts](#http-strict-transport-security-protocol-hsts)）用于向客户端发送 HTTP 严格传输安全协议（HSTS）标头。</span><span class="sxs-lookup"><span data-stu-id="ece97-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="ece97-134">使用反向代理配置部署的应用允许代理处理连接安全（HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="ece97-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="ece97-135">如果代理还处理 HTTPS 重定向，则无需使用 HTTPS 重定向中间件。</span><span class="sxs-lookup"><span data-stu-id="ece97-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="ece97-136">如果代理服务器还处理写入 HSTS 标头（例如， [IIS 10.0 （1709）或更高版本中的本机 HSTS 支持](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)），则应用程序不需要 HSTS 中间件。</span><span class="sxs-lookup"><span data-stu-id="ece97-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="ece97-137">有关详细信息，请参阅[在创建项目时选择退出 HTTPS/HSTS](#opt-out-of-httpshsts-on-project-creation)。</span><span class="sxs-lookup"><span data-stu-id="ece97-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="ece97-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="ece97-138">UseHttpsRedirection</span></span>

<span data-ttu-id="ece97-139">下面的代码调用 `Startup` 类中 `UseHttpsRedirection`：</span><span class="sxs-lookup"><span data-stu-id="ece97-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="ece97-140">前面突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="ece97-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="ece97-141">使用默认的[HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) （[Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)）。</span><span class="sxs-lookup"><span data-stu-id="ece97-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="ece97-142">除非由 `ASPNETCORE_HTTPS_PORT` 环境变量或[IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)重写，否则将使用默认的 HttpsPort （null） [。](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport)</span><span class="sxs-lookup"><span data-stu-id="ece97-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="ece97-143">建议使用临时重定向，而不是永久重定向。</span><span class="sxs-lookup"><span data-stu-id="ece97-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="ece97-144">链接缓存会导致开发环境中的行为不稳定。</span><span class="sxs-lookup"><span data-stu-id="ece97-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="ece97-145">如果希望在应用处于非开发环境中时发送永久重定向状态代码，请参阅在[生产中配置永久重定向](#configure-permanent-redirects-in-production)部分。</span><span class="sxs-lookup"><span data-stu-id="ece97-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="ece97-146">建议使用[HSTS](#http-strict-transport-security-protocol-hsts)向仅应将安全资源请求发送到应用的客户端发送信号（仅在生产中）。</span><span class="sxs-lookup"><span data-stu-id="ece97-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="ece97-147">端口配置</span><span class="sxs-lookup"><span data-stu-id="ece97-147">Port configuration</span></span>

<span data-ttu-id="ece97-148">端口必须可用于中间件，以将不安全的请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="ece97-149">如果没有可用的端口：</span><span class="sxs-lookup"><span data-stu-id="ece97-149">If no port is available:</span></span>

* <span data-ttu-id="ece97-150">不会重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="ece97-151">中间件记录警告 "无法确定用于重定向的 https 端口"。</span><span class="sxs-lookup"><span data-stu-id="ece97-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="ece97-152">使用以下任一方法指定 HTTPS 端口：</span><span class="sxs-lookup"><span data-stu-id="ece97-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="ece97-153">设置[HttpsRedirectionOptions. HttpsPort](#options)。</span><span class="sxs-lookup"><span data-stu-id="ece97-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="ece97-154">设置 "`https_port`[主机" 设置](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)：</span><span class="sxs-lookup"><span data-stu-id="ece97-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="ece97-155">在 "主机配置" 中。</span><span class="sxs-lookup"><span data-stu-id="ece97-155">In host configuration.</span></span>
  * <span data-ttu-id="ece97-156">通过设置 `ASPNETCORE_HTTPS_PORT` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="ece97-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="ece97-157">通过在*appsettings*中添加顶级条目：</span><span class="sxs-lookup"><span data-stu-id="ece97-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="ece97-158">使用[ASPNETCORE_URLS 环境变量](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls)指示包含安全方案的端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="ece97-159">环境变量配置服务器。</span><span class="sxs-lookup"><span data-stu-id="ece97-159">The environment variable configures the server.</span></span> <span data-ttu-id="ece97-160">中间件通过 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>来间接发现 HTTPS 端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="ece97-161">此方法在反向代理部署中不起作用。</span><span class="sxs-lookup"><span data-stu-id="ece97-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="ece97-162">设置 "`https_port`[主机" 设置](xref:fundamentals/host/web-host#https-port)：</span><span class="sxs-lookup"><span data-stu-id="ece97-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="ece97-163">在 "主机配置" 中。</span><span class="sxs-lookup"><span data-stu-id="ece97-163">In host configuration.</span></span>
  * <span data-ttu-id="ece97-164">通过设置 `ASPNETCORE_HTTPS_PORT` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="ece97-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="ece97-165">通过在*appsettings*中添加顶级条目：</span><span class="sxs-lookup"><span data-stu-id="ece97-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="ece97-166">使用[ASPNETCORE_URLS 环境变量](xref:fundamentals/host/web-host#server-urls)指示包含安全方案的端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="ece97-167">环境变量配置服务器。</span><span class="sxs-lookup"><span data-stu-id="ece97-167">The environment variable configures the server.</span></span> <span data-ttu-id="ece97-168">中间件通过 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>来间接发现 HTTPS 端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="ece97-169">此方法在反向代理部署中不起作用。</span><span class="sxs-lookup"><span data-stu-id="ece97-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="ece97-170">在开发中，在*launchsettings.json*中设置 HTTPS URL。</span><span class="sxs-lookup"><span data-stu-id="ece97-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="ece97-171">当使用 IIS Express 时，启用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="ece97-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="ece97-172">为[Kestrel](xref:fundamentals/servers/kestrel) Server 或[http.sys](xref:fundamentals/servers/httpsys)服务器的面向公众的边缘部署配置 HTTPS URL 终结点。</span><span class="sxs-lookup"><span data-stu-id="ece97-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="ece97-173">此应用只使用**一个 HTTPS 端口**。</span><span class="sxs-lookup"><span data-stu-id="ece97-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="ece97-174">中间件通过 <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>发现端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="ece97-175">在反向代理配置中运行应用时，<xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> 不可用。</span><span class="sxs-lookup"><span data-stu-id="ece97-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="ece97-176">使用本部分中所述的其他方法之一设置端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="ece97-177">Edge 部署</span><span class="sxs-lookup"><span data-stu-id="ece97-177">Edge deployments</span></span> 

<span data-ttu-id="ece97-178">当 Kestrel 或 http.sys 用作面向公众的边缘服务器时，必须将 Kestrel 或 http.sys 配置为侦听两者：</span><span class="sxs-lookup"><span data-stu-id="ece97-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="ece97-179">重定向客户端的安全端口（通常为 5001 443）。</span><span class="sxs-lookup"><span data-stu-id="ece97-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="ece97-180">不安全端口（在生产5000环境中通常为80）。</span><span class="sxs-lookup"><span data-stu-id="ece97-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="ece97-181">客户端必须能够访问不安全的端口，以便应用接收不安全的请求，并将客户端重定向到安全端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="ece97-182">有关详细信息，请参阅[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)或 <xref:fundamentals/servers/httpsys>。</span><span class="sxs-lookup"><span data-stu-id="ece97-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="ece97-183">部署方案</span><span class="sxs-lookup"><span data-stu-id="ece97-183">Deployment scenarios</span></span>

<span data-ttu-id="ece97-184">客户端和服务器之间的任何防火墙都必须为流量打开通信端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="ece97-185">如果在反向代理配置中转发请求，请在调用 HTTPS 重定向中间件前使用[转发的标头中间件](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="ece97-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="ece97-186">转发的标头中间件使用 `X-Forwarded-Proto` 标头更新 `Request.Scheme`。</span><span class="sxs-lookup"><span data-stu-id="ece97-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="ece97-187">中间件允许重定向 Uri 和其他安全策略正常工作。</span><span class="sxs-lookup"><span data-stu-id="ece97-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="ece97-188">当未使用转发的标头中间件时，后端应用程序可能无法接收正确的方案并最终出现在重定向循环中。</span><span class="sxs-lookup"><span data-stu-id="ece97-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="ece97-189">常见的最终用户错误消息是发生了太多的重定向。</span><span class="sxs-lookup"><span data-stu-id="ece97-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="ece97-190">部署到 Azure App Service 时，请按照[教程：将现有的自定义 SSL 证书绑定到 Azure Web 应用](/azure/app-service/app-service-web-tutorial-custom-ssl)中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="ece97-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="ece97-191">选项</span><span class="sxs-lookup"><span data-stu-id="ece97-191">Options</span></span>

<span data-ttu-id="ece97-192">以下突出显示的代码调用[AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection)来配置中间件选项：</span><span class="sxs-lookup"><span data-stu-id="ece97-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="ece97-193">仅在更改 `HttpsPort` 或 `RedirectStatusCode`的值时，才需要调用 `AddHttpsRedirection`。</span><span class="sxs-lookup"><span data-stu-id="ece97-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="ece97-194">前面突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="ece97-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="ece97-195">将[HttpsRedirectionOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*)设置为 <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>，这是默认值。</span><span class="sxs-lookup"><span data-stu-id="ece97-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="ece97-196">使用 <xref:Microsoft.AspNetCore.Http.StatusCodes> 类的字段来分配 `RedirectStatusCode`。</span><span class="sxs-lookup"><span data-stu-id="ece97-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="ece97-197">将 HTTPS 端口设置为5001。</span><span class="sxs-lookup"><span data-stu-id="ece97-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="ece97-198">在生产环境中配置永久重定向</span><span class="sxs-lookup"><span data-stu-id="ece97-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="ece97-199">中间件默认为通过所有重定向发送[Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) 。</span><span class="sxs-lookup"><span data-stu-id="ece97-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="ece97-200">如果希望在应用处于非开发环境中时发送永久重定向状态代码，请在非开发环境的条件检查中包装中间件选项配置。</span><span class="sxs-lookup"><span data-stu-id="ece97-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ece97-201">在*Startup.cs*中配置服务时：</span><span class="sxs-lookup"><span data-stu-id="ece97-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
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

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ece97-202">在*Startup.cs*中配置服务时：</span><span class="sxs-lookup"><span data-stu-id="ece97-202">When configuring services in *Startup.cs*:</span></span>

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

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="ece97-203">HTTPS 重定向中间件备用方法</span><span class="sxs-lookup"><span data-stu-id="ece97-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="ece97-204">使用 HTTPS 重定向中间件（`UseHttpsRedirection`）的一种替代方法是使用 URL 重写中间件（`AddRedirectToHttps`）。</span><span class="sxs-lookup"><span data-stu-id="ece97-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="ece97-205">`AddRedirectToHttps` 还可以在执行重定向时设置状态代码和端口。</span><span class="sxs-lookup"><span data-stu-id="ece97-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="ece97-206">有关详细信息，请参阅[URL 重写中间件](xref:fundamentals/url-rewriting)。</span><span class="sxs-lookup"><span data-stu-id="ece97-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="ece97-207">重定向到 HTTPS 时，如果不需要其他重定向规则，我们建议使用本主题中介绍的 HTTPS 重定向中间件（`UseHttpsRedirection`）。</span><span class="sxs-lookup"><span data-stu-id="ece97-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="ece97-208">HTTP 严格传输安全协议（HSTS）</span><span class="sxs-lookup"><span data-stu-id="ece97-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="ece97-209">根据[OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)， [HTTP 严格传输安全（HSTS）](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html)是由 web 应用通过使用响应标头指定的选择加入安全增强功能。</span><span class="sxs-lookup"><span data-stu-id="ece97-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="ece97-210">当[支持 HSTS 的浏览器](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support)收到此标头时：</span><span class="sxs-lookup"><span data-stu-id="ece97-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="ece97-211">浏览器存储域的配置，阻止通过 HTTP 发送任何通信。</span><span class="sxs-lookup"><span data-stu-id="ece97-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="ece97-212">浏览器强制通过 HTTPS 进行的所有通信。</span><span class="sxs-lookup"><span data-stu-id="ece97-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="ece97-213">浏览器阻止用户使用不受信任或无效的证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="ece97-214">浏览器将禁用允许用户暂时信任此类证书的提示。</span><span class="sxs-lookup"><span data-stu-id="ece97-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="ece97-215">由于 HSTS 是由客户端强制执行的，因此存在一些限制：</span><span class="sxs-lookup"><span data-stu-id="ece97-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="ece97-216">客户端必须支持 HSTS。</span><span class="sxs-lookup"><span data-stu-id="ece97-216">The client must support HSTS.</span></span>
* <span data-ttu-id="ece97-217">HSTS 需要至少一个成功的 HTTPS 请求才能建立 HSTS 策略。</span><span class="sxs-lookup"><span data-stu-id="ece97-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="ece97-218">应用程序必须检查每个 HTTP 请求并重定向或拒绝 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="ece97-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="ece97-219">ASP.NET Core 2.1 和更高版本通过 `UseHsts` 扩展方法实现 HSTS。</span><span class="sxs-lookup"><span data-stu-id="ece97-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="ece97-220">当应用未处于[开发模式](xref:fundamentals/environments)时，以下代码将调用 `UseHsts`：</span><span class="sxs-lookup"><span data-stu-id="ece97-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="ece97-221">由于 HSTS 设置由浏览器高度缓存，因此不建议在开发中使用。 `UseHsts`</span><span class="sxs-lookup"><span data-stu-id="ece97-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="ece97-222">默认情况下，`UseHsts` 会排除本地环回地址。</span><span class="sxs-lookup"><span data-stu-id="ece97-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="ece97-223">对于第一次实现 HTTPS 的生产环境，请使用 <xref:System.TimeSpan> 方法之一将初始[HstsOptions](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*)设置为较小的值。</span><span class="sxs-lookup"><span data-stu-id="ece97-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="ece97-224">将值从小时设置为不超过一天，以防需要将 HTTPS 基础结构还原到 HTTP。</span><span class="sxs-lookup"><span data-stu-id="ece97-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="ece97-225">在你确信 HTTPS 配置的可持续性后，请增加 HSTS `max-age` 值;常用值为一年。</span><span class="sxs-lookup"><span data-stu-id="ece97-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="ece97-226">以下代码：</span><span class="sxs-lookup"><span data-stu-id="ece97-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="ece97-227">设置 `Strict-Transport-Security` 标头的预载参数。</span><span class="sxs-lookup"><span data-stu-id="ece97-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="ece97-228">预加载不属于[RFC HSTS 规范](https://tools.ietf.org/html/rfc6797)，但 web 浏览器支持在全新安装时预加载 HSTS 站点。</span><span class="sxs-lookup"><span data-stu-id="ece97-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="ece97-229">有关详细信息，请参阅 [https://hstspreload.org/](https://hstspreload.org/)。</span><span class="sxs-lookup"><span data-stu-id="ece97-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="ece97-230">启用[includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2)，这会将 HSTS 策略应用到托管子域。</span><span class="sxs-lookup"><span data-stu-id="ece97-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="ece97-231">将 `Strict-Transport-Security` 标头的 `max-age` 参数显式设置为60天。</span><span class="sxs-lookup"><span data-stu-id="ece97-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="ece97-232">如果未设置，则默认值为30天。</span><span class="sxs-lookup"><span data-stu-id="ece97-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="ece97-233">有关详细信息，请参阅[最大期限指令](https://tools.ietf.org/html/rfc6797#section-6.1.1)。</span><span class="sxs-lookup"><span data-stu-id="ece97-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="ece97-234">将 `example.com` 添加到要排除的主机列表。</span><span class="sxs-lookup"><span data-stu-id="ece97-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="ece97-235">`UseHsts` 排除以下环回主机：</span><span class="sxs-lookup"><span data-stu-id="ece97-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="ece97-236">`localhost`： IPv4 环回地址。</span><span class="sxs-lookup"><span data-stu-id="ece97-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="ece97-237">`127.0.0.1`： IPv4 环回地址。</span><span class="sxs-lookup"><span data-stu-id="ece97-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="ece97-238">`[::1]`： IPv6 环回地址。</span><span class="sxs-lookup"><span data-stu-id="ece97-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="ece97-239">在项目创建时选择退出 HTTPS/HSTS</span><span class="sxs-lookup"><span data-stu-id="ece97-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="ece97-240">在某些后端服务方案中，如果在网络面向公众的边缘处理连接安全，则不需要在每个节点上配置连接安全性。</span><span class="sxs-lookup"><span data-stu-id="ece97-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="ece97-241">从 Visual Studio 中的模板或从[dotnet new](/dotnet/core/tools/dotnet-new)命令生成的 Web 应用启用[HTTPS 重定向](#require-https)和[HSTS](#http-strict-transport-security-protocol-hsts)。</span><span class="sxs-lookup"><span data-stu-id="ece97-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="ece97-242">对于不需要这些方案的部署，可以从模板创建应用时选择退出 HTTPS/HSTS。</span><span class="sxs-lookup"><span data-stu-id="ece97-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="ece97-243">选择退出 HTTPS/HSTS：</span><span class="sxs-lookup"><span data-stu-id="ece97-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ece97-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ece97-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="ece97-245">取消选中 "**为 HTTPS 配置**" 复选框。</span><span class="sxs-lookup"><span data-stu-id="ece97-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

!["新建 ASP.NET Core Web 应用程序" 对话框，其中显示未选择 "配置为 HTTPS" 复选框。](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

!["新建 ASP.NET Core Web 应用程序" 对话框，其中显示未选择 "配置为 HTTPS" 复选框。](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="ece97-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ece97-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="ece97-249">使用 `--no-https` 选项。</span><span class="sxs-lookup"><span data-stu-id="ece97-249">Use the `--no-https` option.</span></span> <span data-ttu-id="ece97-250">例如：</span><span class="sxs-lookup"><span data-stu-id="ece97-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="ece97-251">信任 Windows 和 macOS 上的 ASP.NET Core HTTPS 开发证书</span><span class="sxs-lookup"><span data-stu-id="ece97-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="ece97-252">.NET Core SDK 包含 HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="ece97-253">此证书作为首次运行体验的一部分进行安装。</span><span class="sxs-lookup"><span data-stu-id="ece97-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="ece97-254">例如，`dotnet --info` 生成以下输出的变体：</span><span class="sxs-lookup"><span data-stu-id="ece97-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="ece97-255">安装 .NET Core SDK 会将 ASP.NET Core HTTPS 开发证书安装到本地用户证书存储。</span><span class="sxs-lookup"><span data-stu-id="ece97-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="ece97-256">已安装证书，但该证书不受信任。</span><span class="sxs-lookup"><span data-stu-id="ece97-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="ece97-257">若要信任该证书，请执行一次性步骤以运行 dotnet `dev-certs` 工具：</span><span class="sxs-lookup"><span data-stu-id="ece97-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="ece97-258">下面的命令提供有关 `dev-certs` 工具的帮助：</span><span class="sxs-lookup"><span data-stu-id="ece97-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="ece97-259">如何为 Docker 设置开发人员证书</span><span class="sxs-lookup"><span data-stu-id="ece97-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="ece97-260">请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/6199)。</span><span class="sxs-lookup"><span data-stu-id="ece97-260">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="ece97-261">从适用于 Linux 的 Windows 子系统信任 HTTPS 证书</span><span class="sxs-lookup"><span data-stu-id="ece97-261">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="ece97-262">适用于 Linux 的 Windows 子系统（WSL）生成 HTTPS 自签名证书。若要将 Windows 证书存储配置为信任 WSL 证书，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ece97-262">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="ece97-263">运行以下命令以导出 WSL 生成的证书： `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="ece97-263">Run the following command to export the WSL-generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="ece97-264">在 WSL 窗口中运行以下命令： `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="ece97-264">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="ece97-265">上述命令将设置环境变量，以便 Linux 使用 Windows 受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-265">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="ece97-266">排查证书问题</span><span class="sxs-lookup"><span data-stu-id="ece97-266">Troubleshoot certificate problems</span></span>

<span data-ttu-id="ece97-267">本部分提供了在[安装和信任](#trust)ASP.NET Core HTTPS 开发证书时，但仍会出现浏览器警告，指出该证书不受信任。</span><span class="sxs-lookup"><span data-stu-id="ece97-267">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="ece97-268">[Kestrel](xref:fundamentals/servers/kestrel)使用 ASP.NET Core HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-268">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="ece97-269">所有平台-证书不受信任</span><span class="sxs-lookup"><span data-stu-id="ece97-269">All platforms - certificate not trusted</span></span>

<span data-ttu-id="ece97-270">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ece97-270">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ece97-271">关闭所有打开的浏览器实例。</span><span class="sxs-lookup"><span data-stu-id="ece97-271">Close any browser instances open.</span></span> <span data-ttu-id="ece97-272">在应用程序中打开新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="ece97-272">Open a new browser window to app.</span></span> <span data-ttu-id="ece97-273">证书信任由浏览器进行缓存。</span><span class="sxs-lookup"><span data-stu-id="ece97-273">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="ece97-274">前面的命令解决了大多数浏览器信任问题。</span><span class="sxs-lookup"><span data-stu-id="ece97-274">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="ece97-275">如果浏览器仍不信任证书，请遵循以下特定于平台的建议。</span><span class="sxs-lookup"><span data-stu-id="ece97-275">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="ece97-276">Docker-证书不受信任</span><span class="sxs-lookup"><span data-stu-id="ece97-276">Docker - certificate not trusted</span></span>

* <span data-ttu-id="ece97-277">删除*C:\Users\{USER} \AppData\Roaming\ASP.NET\Https*文件夹。</span><span class="sxs-lookup"><span data-stu-id="ece97-277">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="ece97-278">清理解决方案。</span><span class="sxs-lookup"><span data-stu-id="ece97-278">Clean the solution.</span></span> <span data-ttu-id="ece97-279">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ece97-279">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="ece97-280">重新启动开发工具。</span><span class="sxs-lookup"><span data-stu-id="ece97-280">Restart the development tool.</span></span> <span data-ttu-id="ece97-281">例如，Visual Studio、Visual Studio Code 或 Visual Studio for Mac。</span><span class="sxs-lookup"><span data-stu-id="ece97-281">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="ece97-282">Windows-证书不受信任</span><span class="sxs-lookup"><span data-stu-id="ece97-282">Windows - certificate not trusted</span></span>

* <span data-ttu-id="ece97-283">检查证书存储区中的证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-283">Check the certificates in the certificate store.</span></span> <span data-ttu-id="ece97-284">在 `Current User > Personal > Certificates` 和 `Current User > Trusted root certification authorities > Certificates` 都应有一个具有 `ASP.NET Core HTTPS development certificate` 友好名称的 `localhost` 证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-284">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="ece97-285">从 "个人" 和 "受信任的根证书颁发机构" 中删除所有找到的证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-285">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="ece97-286">请勿**删除 IIS Express** localhost 证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-286">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="ece97-287">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ece97-287">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ece97-288">关闭所有打开的浏览器实例。</span><span class="sxs-lookup"><span data-stu-id="ece97-288">Close any browser instances open.</span></span> <span data-ttu-id="ece97-289">在应用程序中打开新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="ece97-289">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="ece97-290">OS X-证书不受信任</span><span class="sxs-lookup"><span data-stu-id="ece97-290">OS X - certificate not trusted</span></span>

* <span data-ttu-id="ece97-291">打开密钥链访问。</span><span class="sxs-lookup"><span data-stu-id="ece97-291">Open KeyChain Access.</span></span>
* <span data-ttu-id="ece97-292">选择系统密钥链。</span><span class="sxs-lookup"><span data-stu-id="ece97-292">Select the System keychain.</span></span>
* <span data-ttu-id="ece97-293">检查是否存在 localhost 证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-293">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="ece97-294">检查它是否在图标上包含 `+` 符号，以指示其对所有用户都是受信任的。</span><span class="sxs-lookup"><span data-stu-id="ece97-294">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="ece97-295">从系统密钥链中删除证书。</span><span class="sxs-lookup"><span data-stu-id="ece97-295">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="ece97-296">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ece97-296">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="ece97-297">关闭所有打开的浏览器实例。</span><span class="sxs-lookup"><span data-stu-id="ece97-297">Close any browser instances open.</span></span> <span data-ttu-id="ece97-298">在应用程序中打开新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="ece97-298">Open a new browser window to app.</span></span>

<span data-ttu-id="ece97-299">若要解决 Visual Studio 的证书问题，请参阅[使用 IIS Express （dotnet/AspNetCore #16892）进行 HTTPS 错误](https://github.com/dotnet/AspNetCore/issues/16892)。</span><span class="sxs-lookup"><span data-stu-id="ece97-299">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="ece97-300">用于 Visual Studio 的 IIS Express SSL 证书</span><span class="sxs-lookup"><span data-stu-id="ece97-300">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="ece97-301">若要解决 IIS Express 证书的问题，请从 Visual Studio 安装程序中选择 "**修复**"。</span><span class="sxs-lookup"><span data-stu-id="ece97-301">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="ece97-302">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/16892)。</span><span class="sxs-lookup"><span data-stu-id="ece97-302">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="ece97-303">其他信息</span><span class="sxs-lookup"><span data-stu-id="ece97-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="ece97-304">在 Linux 上利用 Apache： HTTPS 配置托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ece97-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="ece97-305">Nginx 上的主机 ASP.NET Core： HTTPS 配置</span><span class="sxs-lookup"><span data-stu-id="ece97-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="ece97-306">如何在 IIS 上设置 SSL</span><span class="sxs-lookup"><span data-stu-id="ece97-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="ece97-307">OWASP HSTS 浏览器支持</span><span class="sxs-lookup"><span data-stu-id="ece97-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
