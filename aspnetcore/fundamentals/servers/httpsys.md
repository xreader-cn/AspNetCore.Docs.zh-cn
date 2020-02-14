---
title: ASP.NET Core 中的 HTTP.sys Web 服务器实现
author: guardrex
description: 了解 Windows 上适用于 ASP.NET Core 的 Web 服务器 HTTP.sys。 HTTP.sys 构建于 HTTP.sys 内核模式驱动程序之上，是 Kestrel 的一种替代选择，可用来直接连接到 Internet，而无需使用 IIS。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 8a315d859fa70d97501156ff3cf97d4c3a5c5bd4
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171771"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="07e6d-104">ASP.NET Core 中的 HTTP.sys Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="07e6d-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="07e6d-105">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="07e6d-105">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="07e6d-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="07e6d-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="07e6d-108">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-109">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="07e6d-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="07e6d-110">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="07e6d-111">端口共享</span><span class="sxs-lookup"><span data-stu-id="07e6d-111">Port sharing</span></span>
* <span data-ttu-id="07e6d-112">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="07e6d-112">HTTPS with SNI</span></span>
* <span data-ttu-id="07e6d-113">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="07e6d-114">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="07e6d-114">Direct file transmission</span></span>
* <span data-ttu-id="07e6d-115">响应缓存</span><span class="sxs-lookup"><span data-stu-id="07e6d-115">Response caching</span></span>
* <span data-ttu-id="07e6d-116">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="07e6d-117">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="07e6d-117">Supported Windows versions:</span></span>

* <span data-ttu-id="07e6d-118">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-118">Windows 7 or later</span></span>
* <span data-ttu-id="07e6d-119">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="07e6d-120">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07e6d-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="07e6d-121">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-121">When to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-122">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="07e6d-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="07e6d-123">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="07e6d-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="07e6d-125">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="07e6d-127">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="07e6d-128">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="07e6d-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="07e6d-129">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="07e6d-129">HTTP/2 support</span></span>

<span data-ttu-id="07e6d-130">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="07e6d-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="07e6d-131">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="07e6d-132">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="07e6d-133">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="07e6d-134">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="07e6d-135">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="07e6d-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="07e6d-136">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="07e6d-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="07e6d-137">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="07e6d-138">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="07e6d-139">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="07e6d-140">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="07e6d-141">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="07e6d-142">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="07e6d-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="07e6d-143">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="07e6d-144">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-145">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="07e6d-146">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="07e6d-147">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="07e6d-148">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="07e6d-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="07e6d-149">Property</span><span class="sxs-lookup"><span data-stu-id="07e6d-149">Property</span></span> | <span data-ttu-id="07e6d-150">描述</span><span class="sxs-lookup"><span data-stu-id="07e6d-150">Description</span></span> | <span data-ttu-id="07e6d-151">默认</span><span class="sxs-lookup"><span data-stu-id="07e6d-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="07e6d-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="07e6d-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="07e6d-153">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="07e6d-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="07e6d-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="07e6d-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="07e6d-155">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="07e6d-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="07e6d-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="07e6d-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="07e6d-157">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="07e6d-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="07e6d-158">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="07e6d-159">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="07e6d-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="07e6d-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="07e6d-161">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="07e6d-162">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="07e6d-163">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="07e6d-164">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="07e6d-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="07e6d-165">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="07e6d-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="07e6d-166">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="07e6d-167">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-167">Use `-1` for infinite.</span></span> <span data-ttu-id="07e6d-168">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="07e6d-169">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="07e6d-169">(machine-wide</span></span><br><span data-ttu-id="07e6d-170">设置）</span><span class="sxs-lookup"><span data-stu-id="07e6d-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="07e6d-171">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="07e6d-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="07e6d-172">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="07e6d-172">30000000 bytes</span></span><br><span data-ttu-id="07e6d-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="07e6d-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="07e6d-174">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="07e6d-175">1000</span><span class="sxs-lookup"><span data-stu-id="07e6d-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="07e6d-176">这指示服务器是否负责创建和配置请求队列，或是否应附加到现有队列。</span><span class="sxs-lookup"><span data-stu-id="07e6d-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="07e6d-177">附加到现有队列时，大多数现有配置选项不适用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="07e6d-178">HTTP.sys 请求队列的名称。</span><span class="sxs-lookup"><span data-stu-id="07e6d-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="07e6d-179">`null`（匿名队列）</span><span class="sxs-lookup"><span data-stu-id="07e6d-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="07e6d-180">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="07e6d-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="07e6d-181">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="07e6d-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="07e6d-182">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="07e6d-183">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="07e6d-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP 服务器 API 对保持的连接消耗实体正文的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="07e6d-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 请求实体正文到达的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="07e6d-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP 服务器 API 分析请求标头的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="07e6d-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 空闲连接存在的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="07e6d-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="07e6d-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="07e6d-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 请求在被应用选择前在请求队列中保留的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="07e6d-190">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="07e6d-191">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="07e6d-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="07e6d-192">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="07e6d-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="07e6d-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="07e6d-194">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="07e6d-195">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="07e6d-196">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="07e6d-197">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="07e6d-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07e6d-198">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="07e6d-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="07e6d-199">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07e6d-200">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="07e6d-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="07e6d-201">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="07e6d-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-202">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="07e6d-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="07e6d-203">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="07e6d-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="07e6d-205">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="07e6d-205">Configure Windows Server</span></span>

1. <span data-ttu-id="07e6d-206">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="07e6d-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="07e6d-207">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-208">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="07e6d-209">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-210">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="07e6d-211">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="07e6d-212">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="07e6d-213">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书   。</span><span class="sxs-lookup"><span data-stu-id="07e6d-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="07e6d-214">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="07e6d-215">**.NET Core** &ndash; 如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-215">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="07e6d-216">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="07e6d-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="07e6d-217">**.NET Framework** &ndash; 如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-217">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="07e6d-218">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="07e6d-218">Install the required .NET Framework.</span></span> <span data-ttu-id="07e6d-219">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="07e6d-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="07e6d-220">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="07e6d-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="07e6d-221">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="07e6d-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="07e6d-222">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="07e6d-223">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="07e6d-224">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="07e6d-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="07e6d-225">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="07e6d-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="07e6d-226">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="07e6d-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="07e6d-227">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="07e6d-228">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="07e6d-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="07e6d-229">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="07e6d-230">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="07e6d-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="07e6d-231">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="07e6d-232">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="07e6d-233">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="07e6d-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="07e6d-234">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="07e6d-235">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="07e6d-236">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="07e6d-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="07e6d-237">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="07e6d-238">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="07e6d-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="07e6d-239">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="07e6d-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="07e6d-240">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="07e6d-241">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="07e6d-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="07e6d-242">使用 netsh.exe  工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="07e6d-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="07e6d-243">`<URL>` &ndash; 完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-243">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="07e6d-244">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-245">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="07e6d-246">URL 必须包含尾部反斜杠。 </span><span class="sxs-lookup"><span data-stu-id="07e6d-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="07e6d-247">`<USER>` &ndash; 指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="07e6d-247">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="07e6d-248">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="07e6d-249">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="07e6d-250">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="07e6d-251">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="07e6d-252">使用 netsh.exe  工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="07e6d-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="07e6d-253">`<IP>` &ndash; 指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-253">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="07e6d-254">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-255">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="07e6d-256">`<PORT>` &ndash; 指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-256">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="07e6d-257">`<THUMBPRINT>` &ndash; X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="07e6d-257">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="07e6d-258">`<GUID>` &ndash; 开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="07e6d-258">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="07e6d-259">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="07e6d-260">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-260">In Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-261">在“解决方案资源管理器”  中，右键单击应用，并选择“属性”  ，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="07e6d-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="07e6d-262">选择“包”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="07e6d-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="07e6d-263">在“标记”  字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="07e6d-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="07e6d-264">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="07e6d-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-265">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="07e6d-265">Open the app's project file.</span></span>
     * <span data-ttu-id="07e6d-266">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="07e6d-267">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-267">In the following example:</span></span>

   * <span data-ttu-id="07e6d-268">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="07e6d-269">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="07e6d-270">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="07e6d-271">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="07e6d-272">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="07e6d-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="07e6d-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="07e6d-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="07e6d-274">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="07e6d-274">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="07e6d-275">运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-275">Run the app.</span></span>

   <span data-ttu-id="07e6d-276">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="07e6d-277">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="07e6d-278">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="07e6d-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="07e6d-279">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="07e6d-280">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-280">A development certificate is used in this example.</span></span> <span data-ttu-id="07e6d-281">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="07e6d-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="07e6d-283">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="07e6d-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="07e6d-284">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="07e6d-285">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07e6d-286">其他资源</span><span class="sxs-lookup"><span data-stu-id="07e6d-286">Additional resources</span></span>

* [<span data-ttu-id="07e6d-287">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-287">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="07e6d-288">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="07e6d-288">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="07e6d-289">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="07e6d-289">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="07e6d-290">主机</span><span class="sxs-lookup"><span data-stu-id="07e6d-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="07e6d-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="07e6d-292">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="07e6d-293">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-294">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="07e6d-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="07e6d-295">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="07e6d-296">端口共享</span><span class="sxs-lookup"><span data-stu-id="07e6d-296">Port sharing</span></span>
* <span data-ttu-id="07e6d-297">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="07e6d-297">HTTPS with SNI</span></span>
* <span data-ttu-id="07e6d-298">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="07e6d-299">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="07e6d-299">Direct file transmission</span></span>
* <span data-ttu-id="07e6d-300">响应缓存</span><span class="sxs-lookup"><span data-stu-id="07e6d-300">Response caching</span></span>
* <span data-ttu-id="07e6d-301">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="07e6d-302">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="07e6d-302">Supported Windows versions:</span></span>

* <span data-ttu-id="07e6d-303">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-303">Windows 7 or later</span></span>
* <span data-ttu-id="07e6d-304">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="07e6d-305">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07e6d-305">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="07e6d-306">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-306">When to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-307">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="07e6d-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="07e6d-308">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="07e6d-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="07e6d-310">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="07e6d-312">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="07e6d-313">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="07e6d-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="07e6d-314">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="07e6d-314">HTTP/2 support</span></span>

<span data-ttu-id="07e6d-315">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="07e6d-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="07e6d-316">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="07e6d-317">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="07e6d-318">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="07e6d-319">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="07e6d-320">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="07e6d-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="07e6d-321">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="07e6d-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="07e6d-322">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="07e6d-323">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="07e6d-324">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="07e6d-325">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="07e6d-326">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="07e6d-327">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="07e6d-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="07e6d-328">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="07e6d-329">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-330">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="07e6d-331">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="07e6d-332">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="07e6d-333">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="07e6d-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="07e6d-334">Property</span><span class="sxs-lookup"><span data-stu-id="07e6d-334">Property</span></span> | <span data-ttu-id="07e6d-335">描述</span><span class="sxs-lookup"><span data-stu-id="07e6d-335">Description</span></span> | <span data-ttu-id="07e6d-336">默认</span><span class="sxs-lookup"><span data-stu-id="07e6d-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="07e6d-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="07e6d-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="07e6d-338">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="07e6d-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="07e6d-339">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="07e6d-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="07e6d-340">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="07e6d-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="07e6d-341">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="07e6d-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="07e6d-342">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="07e6d-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="07e6d-343">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="07e6d-344">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="07e6d-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="07e6d-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="07e6d-346">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="07e6d-347">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="07e6d-348">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="07e6d-349">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="07e6d-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="07e6d-350">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="07e6d-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="07e6d-351">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="07e6d-352">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-352">Use `-1` for infinite.</span></span> <span data-ttu-id="07e6d-353">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="07e6d-354">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="07e6d-354">(machine-wide</span></span><br><span data-ttu-id="07e6d-355">设置）</span><span class="sxs-lookup"><span data-stu-id="07e6d-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="07e6d-356">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="07e6d-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="07e6d-357">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="07e6d-357">30000000 bytes</span></span><br><span data-ttu-id="07e6d-358">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="07e6d-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="07e6d-359">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="07e6d-360">1000</span><span class="sxs-lookup"><span data-stu-id="07e6d-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="07e6d-361">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="07e6d-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="07e6d-362">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="07e6d-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="07e6d-363">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="07e6d-364">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="07e6d-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP 服务器 API 对保持的连接消耗实体正文的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="07e6d-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 请求实体正文到达的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="07e6d-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP 服务器 API 分析请求标头的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="07e6d-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 空闲连接存在的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="07e6d-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="07e6d-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="07e6d-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 请求在被应用选择前在请求队列中保留的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="07e6d-371">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="07e6d-372">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="07e6d-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="07e6d-373">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="07e6d-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="07e6d-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="07e6d-375">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="07e6d-376">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="07e6d-377">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="07e6d-378">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="07e6d-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07e6d-379">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="07e6d-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="07e6d-380">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07e6d-381">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="07e6d-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="07e6d-382">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="07e6d-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-383">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="07e6d-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="07e6d-384">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="07e6d-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="07e6d-386">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="07e6d-386">Configure Windows Server</span></span>

1. <span data-ttu-id="07e6d-387">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="07e6d-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="07e6d-388">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-389">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="07e6d-390">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-391">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="07e6d-392">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="07e6d-393">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="07e6d-394">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书   。</span><span class="sxs-lookup"><span data-stu-id="07e6d-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="07e6d-395">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="07e6d-396">**.NET Core** &ndash; 如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-396">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="07e6d-397">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="07e6d-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="07e6d-398">**.NET Framework** &ndash; 如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-398">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="07e6d-399">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="07e6d-399">Install the required .NET Framework.</span></span> <span data-ttu-id="07e6d-400">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="07e6d-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="07e6d-401">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="07e6d-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="07e6d-402">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="07e6d-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="07e6d-403">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="07e6d-404">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="07e6d-405">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="07e6d-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="07e6d-406">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="07e6d-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="07e6d-407">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="07e6d-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="07e6d-408">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="07e6d-409">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="07e6d-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="07e6d-410">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="07e6d-411">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="07e6d-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="07e6d-412">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="07e6d-413">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="07e6d-414">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="07e6d-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="07e6d-415">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="07e6d-416">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="07e6d-417">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="07e6d-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="07e6d-418">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="07e6d-419">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="07e6d-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="07e6d-420">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="07e6d-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="07e6d-421">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="07e6d-422">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="07e6d-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="07e6d-423">使用 netsh.exe  工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="07e6d-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="07e6d-424">`<URL>` &ndash; 完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-424">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="07e6d-425">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-426">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="07e6d-427">URL 必须包含尾部反斜杠。 </span><span class="sxs-lookup"><span data-stu-id="07e6d-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="07e6d-428">`<USER>` &ndash; 指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="07e6d-428">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="07e6d-429">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="07e6d-430">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="07e6d-431">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="07e6d-432">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="07e6d-433">使用 netsh.exe  工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="07e6d-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="07e6d-434">`<IP>` &ndash; 指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-434">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="07e6d-435">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-436">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="07e6d-437">`<PORT>` &ndash; 指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-437">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="07e6d-438">`<THUMBPRINT>` &ndash; X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="07e6d-438">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="07e6d-439">`<GUID>` &ndash; 开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="07e6d-439">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="07e6d-440">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="07e6d-441">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-441">In Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-442">在“解决方案资源管理器”  中，右键单击应用，并选择“属性”  ，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="07e6d-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="07e6d-443">选择“包”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="07e6d-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="07e6d-444">在“标记”  字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="07e6d-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="07e6d-445">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="07e6d-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-446">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="07e6d-446">Open the app's project file.</span></span>
     * <span data-ttu-id="07e6d-447">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="07e6d-448">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-448">In the following example:</span></span>

   * <span data-ttu-id="07e6d-449">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="07e6d-450">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="07e6d-451">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="07e6d-452">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="07e6d-453">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="07e6d-453">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="07e6d-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="07e6d-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="07e6d-455">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="07e6d-455">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="07e6d-456">运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-456">Run the app.</span></span>

   <span data-ttu-id="07e6d-457">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="07e6d-458">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="07e6d-459">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="07e6d-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="07e6d-460">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="07e6d-461">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-461">A development certificate is used in this example.</span></span> <span data-ttu-id="07e6d-462">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="07e6d-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="07e6d-464">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="07e6d-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="07e6d-465">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="07e6d-466">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07e6d-467">其他资源</span><span class="sxs-lookup"><span data-stu-id="07e6d-467">Additional resources</span></span>

* [<span data-ttu-id="07e6d-468">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-468">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="07e6d-469">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="07e6d-469">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="07e6d-470">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="07e6d-470">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="07e6d-471">主机</span><span class="sxs-lookup"><span data-stu-id="07e6d-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="07e6d-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="07e6d-473">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="07e6d-474">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-475">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="07e6d-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="07e6d-476">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="07e6d-477">端口共享</span><span class="sxs-lookup"><span data-stu-id="07e6d-477">Port sharing</span></span>
* <span data-ttu-id="07e6d-478">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="07e6d-478">HTTPS with SNI</span></span>
* <span data-ttu-id="07e6d-479">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="07e6d-480">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="07e6d-480">Direct file transmission</span></span>
* <span data-ttu-id="07e6d-481">响应缓存</span><span class="sxs-lookup"><span data-stu-id="07e6d-481">Response caching</span></span>
* <span data-ttu-id="07e6d-482">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="07e6d-483">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="07e6d-483">Supported Windows versions:</span></span>

* <span data-ttu-id="07e6d-484">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-484">Windows 7 or later</span></span>
* <span data-ttu-id="07e6d-485">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="07e6d-486">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07e6d-486">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="07e6d-487">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-487">When to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-488">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="07e6d-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="07e6d-489">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="07e6d-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="07e6d-491">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="07e6d-493">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="07e6d-494">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="07e6d-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="07e6d-495">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="07e6d-495">HTTP/2 support</span></span>

<span data-ttu-id="07e6d-496">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="07e6d-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="07e6d-497">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="07e6d-498">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="07e6d-499">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="07e6d-500">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="07e6d-501">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="07e6d-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="07e6d-502">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="07e6d-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="07e6d-503">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="07e6d-504">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="07e6d-505">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="07e6d-506">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="07e6d-507">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="07e6d-508">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="07e6d-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="07e6d-509">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="07e6d-510">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-511">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="07e6d-512">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="07e6d-513">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="07e6d-514">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="07e6d-515">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="07e6d-516">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="07e6d-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="07e6d-517">Property</span><span class="sxs-lookup"><span data-stu-id="07e6d-517">Property</span></span> | <span data-ttu-id="07e6d-518">描述</span><span class="sxs-lookup"><span data-stu-id="07e6d-518">Description</span></span> | <span data-ttu-id="07e6d-519">默认</span><span class="sxs-lookup"><span data-stu-id="07e6d-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="07e6d-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="07e6d-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="07e6d-521">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="07e6d-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="07e6d-522">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="07e6d-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="07e6d-523">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="07e6d-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="07e6d-524">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="07e6d-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="07e6d-525">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="07e6d-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="07e6d-526">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="07e6d-527">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="07e6d-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="07e6d-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="07e6d-529">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="07e6d-530">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="07e6d-531">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="07e6d-532">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="07e6d-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="07e6d-533">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="07e6d-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="07e6d-534">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="07e6d-535">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-535">Use `-1` for infinite.</span></span> <span data-ttu-id="07e6d-536">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="07e6d-537">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="07e6d-537">(machine-wide</span></span><br><span data-ttu-id="07e6d-538">设置）</span><span class="sxs-lookup"><span data-stu-id="07e6d-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="07e6d-539">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="07e6d-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="07e6d-540">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="07e6d-540">30000000 bytes</span></span><br><span data-ttu-id="07e6d-541">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="07e6d-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="07e6d-542">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="07e6d-543">1000</span><span class="sxs-lookup"><span data-stu-id="07e6d-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="07e6d-544">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="07e6d-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="07e6d-545">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="07e6d-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="07e6d-546">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="07e6d-547">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="07e6d-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP 服务器 API 对保持的连接消耗实体正文的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="07e6d-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 请求实体正文到达的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="07e6d-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP 服务器 API 分析请求标头的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="07e6d-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 空闲连接存在的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="07e6d-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="07e6d-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="07e6d-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 请求在被应用选择前在请求队列中保留的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="07e6d-554">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="07e6d-555">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="07e6d-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="07e6d-556">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="07e6d-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="07e6d-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="07e6d-558">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="07e6d-559">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="07e6d-560">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="07e6d-561">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="07e6d-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07e6d-562">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="07e6d-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="07e6d-563">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07e6d-564">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="07e6d-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="07e6d-565">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="07e6d-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-566">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="07e6d-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="07e6d-567">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="07e6d-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="07e6d-569">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="07e6d-569">Configure Windows Server</span></span>

1. <span data-ttu-id="07e6d-570">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="07e6d-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="07e6d-571">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-572">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="07e6d-573">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-574">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="07e6d-575">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="07e6d-576">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="07e6d-577">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书   。</span><span class="sxs-lookup"><span data-stu-id="07e6d-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="07e6d-578">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="07e6d-579">**.NET Core** &ndash; 如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-579">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="07e6d-580">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="07e6d-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="07e6d-581">**.NET Framework** &ndash; 如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-581">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="07e6d-582">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="07e6d-582">Install the required .NET Framework.</span></span> <span data-ttu-id="07e6d-583">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="07e6d-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="07e6d-584">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="07e6d-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="07e6d-585">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="07e6d-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="07e6d-586">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="07e6d-587">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="07e6d-588">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="07e6d-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="07e6d-589">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="07e6d-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="07e6d-590">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="07e6d-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="07e6d-591">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="07e6d-592">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="07e6d-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="07e6d-593">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="07e6d-594">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="07e6d-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="07e6d-595">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="07e6d-596">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="07e6d-597">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="07e6d-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="07e6d-598">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="07e6d-599">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="07e6d-600">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="07e6d-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="07e6d-601">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="07e6d-602">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="07e6d-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="07e6d-603">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="07e6d-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="07e6d-604">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="07e6d-605">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="07e6d-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="07e6d-606">使用 netsh.exe  工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="07e6d-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="07e6d-607">`<URL>` &ndash; 完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-607">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="07e6d-608">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-609">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="07e6d-610">URL 必须包含尾部反斜杠。 </span><span class="sxs-lookup"><span data-stu-id="07e6d-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="07e6d-611">`<USER>` &ndash; 指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="07e6d-611">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="07e6d-612">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="07e6d-613">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="07e6d-614">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="07e6d-615">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="07e6d-616">使用 netsh.exe  工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="07e6d-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="07e6d-617">`<IP>` &ndash; 指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-617">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="07e6d-618">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-619">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="07e6d-620">`<PORT>` &ndash; 指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-620">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="07e6d-621">`<THUMBPRINT>` &ndash; X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="07e6d-621">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="07e6d-622">`<GUID>` &ndash; 开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="07e6d-622">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="07e6d-623">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="07e6d-624">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-624">In Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-625">在“解决方案资源管理器”  中，右键单击应用，并选择“属性”  ，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="07e6d-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="07e6d-626">选择“包”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="07e6d-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="07e6d-627">在“标记”  字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="07e6d-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="07e6d-628">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="07e6d-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-629">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="07e6d-629">Open the app's project file.</span></span>
     * <span data-ttu-id="07e6d-630">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="07e6d-631">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-631">In the following example:</span></span>

   * <span data-ttu-id="07e6d-632">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="07e6d-633">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="07e6d-634">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="07e6d-635">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="07e6d-636">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="07e6d-636">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="07e6d-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="07e6d-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="07e6d-638">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="07e6d-638">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="07e6d-639">运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-639">Run the app.</span></span>

   <span data-ttu-id="07e6d-640">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="07e6d-641">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="07e6d-642">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="07e6d-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="07e6d-643">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="07e6d-644">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-644">A development certificate is used in this example.</span></span> <span data-ttu-id="07e6d-645">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="07e6d-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="07e6d-647">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="07e6d-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="07e6d-648">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="07e6d-649">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07e6d-650">其他资源</span><span class="sxs-lookup"><span data-stu-id="07e6d-650">Additional resources</span></span>

* [<span data-ttu-id="07e6d-651">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-651">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="07e6d-652">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="07e6d-652">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="07e6d-653">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="07e6d-653">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="07e6d-654">主机</span><span class="sxs-lookup"><span data-stu-id="07e6d-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="07e6d-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="07e6d-656">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="07e6d-657">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-658">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="07e6d-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="07e6d-659">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="07e6d-660">端口共享</span><span class="sxs-lookup"><span data-stu-id="07e6d-660">Port sharing</span></span>
* <span data-ttu-id="07e6d-661">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="07e6d-661">HTTPS with SNI</span></span>
* <span data-ttu-id="07e6d-662">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="07e6d-663">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="07e6d-663">Direct file transmission</span></span>
* <span data-ttu-id="07e6d-664">响应缓存</span><span class="sxs-lookup"><span data-stu-id="07e6d-664">Response caching</span></span>
* <span data-ttu-id="07e6d-665">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="07e6d-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="07e6d-666">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="07e6d-666">Supported Windows versions:</span></span>

* <span data-ttu-id="07e6d-667">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-667">Windows 7 or later</span></span>
* <span data-ttu-id="07e6d-668">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="07e6d-669">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07e6d-669">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="07e6d-670">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-670">When to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-671">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="07e6d-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="07e6d-672">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="07e6d-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="07e6d-674">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="07e6d-676">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="07e6d-677">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="07e6d-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="07e6d-678">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="07e6d-678">HTTP/2 support</span></span>

<span data-ttu-id="07e6d-679">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="07e6d-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="07e6d-680">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="07e6d-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="07e6d-681">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="07e6d-682">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="07e6d-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="07e6d-683">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="07e6d-684">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="07e6d-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="07e6d-685">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="07e6d-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="07e6d-686">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="07e6d-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="07e6d-687">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="07e6d-688">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="07e6d-689">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="07e6d-690">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="07e6d-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="07e6d-691">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="07e6d-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="07e6d-692">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="07e6d-693">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="07e6d-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="07e6d-694">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="07e6d-695">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="07e6d-696">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="07e6d-697">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="07e6d-698">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="07e6d-699">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="07e6d-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="07e6d-700">Property</span><span class="sxs-lookup"><span data-stu-id="07e6d-700">Property</span></span> | <span data-ttu-id="07e6d-701">描述</span><span class="sxs-lookup"><span data-stu-id="07e6d-701">Description</span></span> | <span data-ttu-id="07e6d-702">默认</span><span class="sxs-lookup"><span data-stu-id="07e6d-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="07e6d-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="07e6d-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="07e6d-704">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="07e6d-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="07e6d-705">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="07e6d-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="07e6d-706">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="07e6d-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="07e6d-707">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="07e6d-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="07e6d-708">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="07e6d-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="07e6d-709">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="07e6d-710">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="07e6d-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="07e6d-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="07e6d-712">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="07e6d-713">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="07e6d-714">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="07e6d-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="07e6d-715">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="07e6d-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="07e6d-716">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="07e6d-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="07e6d-717">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="07e6d-718">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-718">Use `-1` for infinite.</span></span> <span data-ttu-id="07e6d-719">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="07e6d-720">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="07e6d-720">(machine-wide</span></span><br><span data-ttu-id="07e6d-721">设置）</span><span class="sxs-lookup"><span data-stu-id="07e6d-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="07e6d-722">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="07e6d-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="07e6d-723">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="07e6d-723">30000000 bytes</span></span><br><span data-ttu-id="07e6d-724">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="07e6d-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="07e6d-725">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="07e6d-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="07e6d-726">1000</span><span class="sxs-lookup"><span data-stu-id="07e6d-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="07e6d-727">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="07e6d-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="07e6d-728">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="07e6d-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="07e6d-729">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="07e6d-730">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="07e6d-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="07e6d-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP 服务器 API 对保持的连接消耗实体正文的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="07e6d-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 请求实体正文到达的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="07e6d-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP 服务器 API 分析请求标头的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="07e6d-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 空闲连接存在的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="07e6d-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="07e6d-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="07e6d-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 请求在被应用选择前在请求队列中保留的时间上限。</span><span class="sxs-lookup"><span data-stu-id="07e6d-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="07e6d-737">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="07e6d-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="07e6d-738">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="07e6d-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="07e6d-739">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="07e6d-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="07e6d-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="07e6d-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="07e6d-741">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="07e6d-742">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="07e6d-743">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="07e6d-744">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="07e6d-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="07e6d-745">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="07e6d-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="07e6d-746">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="07e6d-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="07e6d-747">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="07e6d-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="07e6d-748">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="07e6d-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="07e6d-749">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="07e6d-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="07e6d-750">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="07e6d-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="07e6d-752">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="07e6d-752">Configure Windows Server</span></span>

1. <span data-ttu-id="07e6d-753">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="07e6d-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="07e6d-754">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-755">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="07e6d-756">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="07e6d-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="07e6d-757">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="07e6d-758">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="07e6d-759">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="07e6d-760">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书   。</span><span class="sxs-lookup"><span data-stu-id="07e6d-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="07e6d-761">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="07e6d-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="07e6d-762">**.NET Core** &ndash; 如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-762">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="07e6d-763">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="07e6d-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="07e6d-764">**.NET Framework** &ndash; 如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-764">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="07e6d-765">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="07e6d-765">Install the required .NET Framework.</span></span> <span data-ttu-id="07e6d-766">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="07e6d-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="07e6d-767">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="07e6d-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="07e6d-768">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="07e6d-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="07e6d-769">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="07e6d-770">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="07e6d-771">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="07e6d-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="07e6d-772">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="07e6d-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="07e6d-773">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="07e6d-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="07e6d-774">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="07e6d-775">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="07e6d-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="07e6d-776">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="07e6d-777">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="07e6d-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="07e6d-778">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="07e6d-779">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）  。</span><span class="sxs-lookup"><span data-stu-id="07e6d-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="07e6d-780">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="07e6d-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="07e6d-781">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="07e6d-782">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="07e6d-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="07e6d-783">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="07e6d-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="07e6d-784">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="07e6d-785">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="07e6d-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="07e6d-786">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="07e6d-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="07e6d-787">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="07e6d-788">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="07e6d-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="07e6d-789">使用 netsh.exe  工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="07e6d-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="07e6d-790">`<URL>` &ndash; 完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-790">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="07e6d-791">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-792">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="07e6d-793">URL 必须包含尾部反斜杠。 </span><span class="sxs-lookup"><span data-stu-id="07e6d-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="07e6d-794">`<USER>` &ndash; 指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="07e6d-794">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="07e6d-795">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="07e6d-796">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="07e6d-797">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="07e6d-798">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="07e6d-799">使用 netsh.exe  工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="07e6d-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="07e6d-800">`<IP>` &ndash; 指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-800">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="07e6d-801">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="07e6d-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="07e6d-802">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="07e6d-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="07e6d-803">`<PORT>` &ndash; 指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="07e6d-803">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="07e6d-804">`<THUMBPRINT>` &ndash; X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="07e6d-804">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="07e6d-805">`<GUID>` &ndash; 开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="07e6d-805">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="07e6d-806">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="07e6d-807">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-807">In Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-808">在“解决方案资源管理器”  中，右键单击应用，并选择“属性”  ，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="07e6d-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="07e6d-809">选择“包”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="07e6d-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="07e6d-810">在“标记”  字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="07e6d-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="07e6d-811">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="07e6d-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="07e6d-812">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="07e6d-812">Open the app's project file.</span></span>
     * <span data-ttu-id="07e6d-813">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="07e6d-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="07e6d-814">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="07e6d-814">In the following example:</span></span>

   * <span data-ttu-id="07e6d-815">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="07e6d-816">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="07e6d-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="07e6d-817">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="07e6d-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="07e6d-818">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="07e6d-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="07e6d-819">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="07e6d-819">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="07e6d-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="07e6d-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="07e6d-821">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="07e6d-821">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="07e6d-822">运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-822">Run the app.</span></span>

   <span data-ttu-id="07e6d-823">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="07e6d-824">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="07e6d-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="07e6d-825">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="07e6d-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="07e6d-826">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="07e6d-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="07e6d-827">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="07e6d-827">A development certificate is used in this example.</span></span> <span data-ttu-id="07e6d-828">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="07e6d-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="07e6d-830">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="07e6d-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="07e6d-831">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="07e6d-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="07e6d-832">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="07e6d-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07e6d-833">其他资源</span><span class="sxs-lookup"><span data-stu-id="07e6d-833">Additional resources</span></span>

* [<span data-ttu-id="07e6d-834">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="07e6d-834">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="07e6d-835">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="07e6d-835">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="07e6d-836">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="07e6d-836">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="07e6d-837">主机</span><span class="sxs-lookup"><span data-stu-id="07e6d-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
