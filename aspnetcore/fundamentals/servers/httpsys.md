---
title: ASP.NET Core 中的 HTTP.sys Web 服务器实现
author: rick-anderson
description: 了解 Windows 上适用于 ASP.NET Core 的 Web 服务器 HTTP.sys。 HTTP.sys 构建于 HTTP.sys 内核模式驱动程序之上，是 Kestrel 的一种替代选择，可用来直接连接到 Internet，而无需使用 IIS。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/httpsys
ms.openlocfilehash: ad37f8434b6025c5f3ec97dc52987f5660a64edc
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106669"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="10c95-104">ASP.NET Core 中的 HTTP.sys Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="10c95-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="10c95-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="10c95-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="10c95-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10c95-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10c95-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10c95-108">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10c95-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-109">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10c95-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10c95-110">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10c95-111">端口共享</span><span class="sxs-lookup"><span data-stu-id="10c95-111">Port sharing</span></span>
* <span data-ttu-id="10c95-112">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10c95-112">HTTPS with SNI</span></span>
* <span data-ttu-id="10c95-113">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10c95-114">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10c95-114">Direct file transmission</span></span>
* <span data-ttu-id="10c95-115">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10c95-115">Response caching</span></span>
* <span data-ttu-id="10c95-116">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10c95-117">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10c95-117">Supported Windows versions:</span></span>

* <span data-ttu-id="10c95-118">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-118">Windows 7 or later</span></span>
* <span data-ttu-id="10c95-119">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10c95-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10c95-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10c95-121">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-121">When to use HTTP.sys</span></span>

<span data-ttu-id="10c95-122">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10c95-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10c95-123">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10c95-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10c95-125">内部部署需要 Kestrel 中没有的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-125">An internal deployment requires a feature not available in Kestrel.</span></span> <span data-ttu-id="10c95-126">有关详细信息，请参阅本文档中的 [Kestrel 与HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span><span class="sxs-lookup"><span data-stu-id="10c95-126">For more information, see [Kestrel vs. HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10c95-128">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-128">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10c95-129">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10c95-129">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10c95-130">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10c95-130">HTTP/2 support</span></span>

<span data-ttu-id="10c95-131">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10c95-131">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10c95-132">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-132">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10c95-133">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10c95-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10c95-134">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10c95-134">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10c95-135">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="10c95-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="10c95-136">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c95-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10c95-137">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10c95-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10c95-138">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10c95-139">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10c95-140">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10c95-141">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10c95-142">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10c95-143">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10c95-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10c95-144">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10c95-145">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10c95-146">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10c95-146">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10c95-147">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-147">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="10c95-148">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-148">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10c95-149">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10c95-149">**HTTP.sys options**</span></span>

| <span data-ttu-id="10c95-150">Property</span><span class="sxs-lookup"><span data-stu-id="10c95-150">Property</span></span> | <span data-ttu-id="10c95-151">描述</span><span class="sxs-lookup"><span data-stu-id="10c95-151">Description</span></span> | <span data-ttu-id="10c95-152">默认</span><span class="sxs-lookup"><span data-stu-id="10c95-152">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="10c95-153">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10c95-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="10c95-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10c95-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10c95-155">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10c95-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10c95-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10c95-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10c95-157">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10c95-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10c95-158">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10c95-159">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10c95-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="10c95-160">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-160">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10c95-161">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-161">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10c95-162">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-162">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="10c95-163">由于限制条件而拒绝请求时的 HTTP.sys 行为。</span><span class="sxs-lookup"><span data-stu-id="10c95-163">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="10c95-164">Http503VerbosityLevel.<br>Basic</span><span class="sxs-lookup"><span data-stu-id="10c95-164">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10c95-165">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10c95-165">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10c95-166">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10c95-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10c95-167">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10c95-167">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10c95-168">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10c95-168">Use `-1` for infinite.</span></span> <span data-ttu-id="10c95-169">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-169">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10c95-170">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10c95-170">(machine-wide</span></span><br><span data-ttu-id="10c95-171">设置）</span><span class="sxs-lookup"><span data-stu-id="10c95-171">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10c95-172">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10c95-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10c95-173">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10c95-173">30000000 bytes</span></span><br><span data-ttu-id="10c95-174">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10c95-174">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10c95-175">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10c95-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10c95-176">1000</span><span class="sxs-lookup"><span data-stu-id="10c95-176">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="10c95-177">这指示服务器是否负责创建和配置请求队列，或是否应附加到现有队列。</span><span class="sxs-lookup"><span data-stu-id="10c95-177">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="10c95-178">附加到现有队列时，大多数现有配置选项不适用。</span><span class="sxs-lookup"><span data-stu-id="10c95-178">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="10c95-179">HTTP.sys 请求队列的名称。</span><span class="sxs-lookup"><span data-stu-id="10c95-179">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="10c95-180">`null`（匿名队列）</span><span class="sxs-lookup"><span data-stu-id="10c95-180">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10c95-181">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10c95-181">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10c95-182">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10c95-182">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10c95-183">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-183">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10c95-184">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-184">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10c95-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10c95-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10c95-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10c95-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10c95-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10c95-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10c95-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10c95-191">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10c95-191">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10c95-192">最有用的是 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10c95-192">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10c95-193">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-193">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10c95-194">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10c95-194">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10c95-195">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10c95-195">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10c95-196">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-196">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10c95-197">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-197">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10c95-198">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10c95-198">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10c95-199">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10c95-199">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10c95-200">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-200">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10c95-201">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10c95-201">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10c95-202">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10c95-202">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-203">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10c95-203">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10c95-204">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10c95-204">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10c95-206">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10c95-206">Configure Windows Server</span></span>

1. <span data-ttu-id="10c95-207">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10c95-207">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10c95-208">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-208">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-209">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-209">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10c95-210">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-210">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-211">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-211">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10c95-212">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-212">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10c95-213">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10c95-213">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10c95-214">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10c95-214">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10c95-215">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10c95-215">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10c95-216">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-216">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10c95-217">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10c95-217">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10c95-218">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10c95-218">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10c95-219">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10c95-219">Install the required .NET Framework.</span></span> <span data-ttu-id="10c95-220">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-220">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10c95-221">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10c95-221">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10c95-222">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10c95-222">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10c95-223">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-223">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10c95-224">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10c95-224">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10c95-225">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10c95-225">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10c95-226">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10c95-226">`urls` command-line argument</span></span>
   * <span data-ttu-id="10c95-227">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10c95-227">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10c95-228">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-228">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="10c95-229">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10c95-229">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10c95-230">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-230">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10c95-231">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10c95-231">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10c95-232">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10c95-232">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10c95-233">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10c95-233">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10c95-234">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10c95-234">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10c95-235">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-235">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10c95-236">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-236">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10c95-237">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10c95-237">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10c95-238">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10c95-238">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10c95-239">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10c95-239">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10c95-240">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10c95-240">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10c95-241">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-241">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10c95-242">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10c95-242">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10c95-243">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10c95-243">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10c95-244">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10c95-244">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10c95-245">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-245">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-246">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-246">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10c95-247">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10c95-247">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10c95-248">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10c95-248">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10c95-249">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-249">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10c95-250">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-250">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10c95-251">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-251">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10c95-252">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-252">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10c95-253">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10c95-253">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10c95-254">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-254">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10c95-255">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-255">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-256">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-256">Use a valid IP address.</span></span>
   * <span data-ttu-id="10c95-257">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-257">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10c95-258">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10c95-258">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10c95-259">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10c95-259">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10c95-260">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10c95-260">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10c95-261">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10c95-261">In Visual Studio:</span></span>
     * <span data-ttu-id="10c95-262">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10c95-262">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10c95-263">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10c95-263">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10c95-264">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10c95-264">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10c95-265">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10c95-265">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10c95-266">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10c95-266">Open the app's project file.</span></span>
     * <span data-ttu-id="10c95-267">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10c95-267">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10c95-268">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10c95-268">In the following example:</span></span>

   * <span data-ttu-id="10c95-269">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10c95-269">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10c95-270">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10c95-270">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10c95-271">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-271">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10c95-272">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-272">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10c95-273">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10c95-273">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10c95-274">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10c95-274">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10c95-275">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10c95-275">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10c95-276">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-276">Run the app.</span></span>

   <span data-ttu-id="10c95-277">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-277">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10c95-278">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-278">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10c95-279">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10c95-279">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10c95-280">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-280">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10c95-281">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-281">A development certificate is used in this example.</span></span> <span data-ttu-id="10c95-282">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10c95-282">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10c95-284">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10c95-284">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10c95-285">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-285">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10c95-286">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10c95-286">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="10c95-287">用于支持 gRPC 的高级 HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="10c95-287">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="10c95-288">HTTP.sys 中的其他 HTTP/2 功能支持 gRPC，包括对响应尾部和发送重置帧的支持。</span><span class="sxs-lookup"><span data-stu-id="10c95-288">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="10c95-289">使用 HTTP.sys 运行 gRPC 的要求：</span><span class="sxs-lookup"><span data-stu-id="10c95-289">Requirements to run gRPC with HTTP.sys:</span></span>

* <span data-ttu-id="10c95-290">Windows 10，OS 内部版本 19041.508 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-290">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="10c95-291">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10c95-291">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="10c95-292">预告片</span><span class="sxs-lookup"><span data-stu-id="10c95-292">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="10c95-293">重置</span><span class="sxs-lookup"><span data-stu-id="10c95-293">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="10c95-294">其他资源</span><span class="sxs-lookup"><span data-stu-id="10c95-294">Additional resources</span></span>

* [<span data-ttu-id="10c95-295">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-295">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10c95-296">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10c95-296">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10c95-297">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10c95-297">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10c95-298">主机</span><span class="sxs-lookup"><span data-stu-id="10c95-298">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="10c95-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10c95-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10c95-300">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-300">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10c95-301">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10c95-301">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-302">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10c95-302">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10c95-303">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-303">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10c95-304">端口共享</span><span class="sxs-lookup"><span data-stu-id="10c95-304">Port sharing</span></span>
* <span data-ttu-id="10c95-305">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10c95-305">HTTPS with SNI</span></span>
* <span data-ttu-id="10c95-306">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-306">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10c95-307">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10c95-307">Direct file transmission</span></span>
* <span data-ttu-id="10c95-308">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10c95-308">Response caching</span></span>
* <span data-ttu-id="10c95-309">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-309">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10c95-310">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10c95-310">Supported Windows versions:</span></span>

* <span data-ttu-id="10c95-311">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-311">Windows 7 or later</span></span>
* <span data-ttu-id="10c95-312">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-312">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10c95-313">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10c95-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10c95-314">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-314">When to use HTTP.sys</span></span>

<span data-ttu-id="10c95-315">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10c95-315">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10c95-316">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10c95-316">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10c95-318">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10c95-318">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10c95-320">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-320">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10c95-321">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10c95-321">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10c95-322">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10c95-322">HTTP/2 support</span></span>

<span data-ttu-id="10c95-323">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10c95-323">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10c95-324">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-324">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10c95-325">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10c95-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10c95-326">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10c95-326">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10c95-327">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="10c95-327">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="10c95-328">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c95-328">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10c95-329">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10c95-329">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10c95-330">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-330">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10c95-331">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-331">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10c95-332">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-332">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10c95-333">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-333">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10c95-334">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-334">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10c95-335">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10c95-335">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10c95-336">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-336">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10c95-337">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-337">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10c95-338">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10c95-338">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10c95-339">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-339">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="10c95-340">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-340">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10c95-341">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10c95-341">**HTTP.sys options**</span></span>

| <span data-ttu-id="10c95-342">Property</span><span class="sxs-lookup"><span data-stu-id="10c95-342">Property</span></span> | <span data-ttu-id="10c95-343">描述</span><span class="sxs-lookup"><span data-stu-id="10c95-343">Description</span></span> | <span data-ttu-id="10c95-344">默认</span><span class="sxs-lookup"><span data-stu-id="10c95-344">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="10c95-345">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="10c95-345">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="10c95-346">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10c95-346">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="10c95-347">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10c95-347">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10c95-348">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10c95-348">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10c95-349">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10c95-349">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10c95-350">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10c95-350">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10c95-351">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-351">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10c95-352">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10c95-352">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="10c95-353">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="10c95-353">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="10c95-354">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-354">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10c95-355">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-355">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10c95-356">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-356">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10c95-357">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10c95-357">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10c95-358">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10c95-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10c95-359">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10c95-359">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10c95-360">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10c95-360">Use `-1` for infinite.</span></span> <span data-ttu-id="10c95-361">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-361">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10c95-362">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10c95-362">(machine-wide</span></span><br><span data-ttu-id="10c95-363">设置）</span><span class="sxs-lookup"><span data-stu-id="10c95-363">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10c95-364">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10c95-364">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10c95-365">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10c95-365">30000000 bytes</span></span><br><span data-ttu-id="10c95-366">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10c95-366">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10c95-367">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10c95-367">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10c95-368">1000</span><span class="sxs-lookup"><span data-stu-id="10c95-368">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10c95-369">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10c95-369">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10c95-370">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10c95-370">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10c95-371">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-371">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10c95-372">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-372">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10c95-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10c95-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10c95-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10c95-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10c95-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10c95-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10c95-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10c95-379">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10c95-379">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10c95-380">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10c95-380">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10c95-381">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-381">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10c95-382">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10c95-382">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10c95-383">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10c95-383">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10c95-384">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-384">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10c95-385">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-385">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10c95-386">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10c95-386">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10c95-387">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10c95-387">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10c95-388">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-388">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10c95-389">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10c95-389">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10c95-390">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10c95-390">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-391">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10c95-391">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10c95-392">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10c95-392">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10c95-394">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10c95-394">Configure Windows Server</span></span>

1. <span data-ttu-id="10c95-395">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10c95-395">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10c95-396">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-396">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-397">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-397">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10c95-398">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-398">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-399">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-399">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10c95-400">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-400">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10c95-401">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10c95-401">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10c95-402">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10c95-402">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10c95-403">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10c95-403">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10c95-404">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-404">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10c95-405">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10c95-405">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10c95-406">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10c95-406">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10c95-407">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10c95-407">Install the required .NET Framework.</span></span> <span data-ttu-id="10c95-408">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-408">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10c95-409">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10c95-409">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10c95-410">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10c95-410">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10c95-411">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-411">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10c95-412">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10c95-412">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10c95-413">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10c95-413">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10c95-414">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10c95-414">`urls` command-line argument</span></span>
   * <span data-ttu-id="10c95-415">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10c95-415">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10c95-416">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-416">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="10c95-417">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10c95-417">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10c95-418">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-418">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10c95-419">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10c95-419">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10c95-420">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10c95-420">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10c95-421">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10c95-421">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10c95-422">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10c95-422">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10c95-423">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-423">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10c95-424">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-424">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10c95-425">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10c95-425">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10c95-426">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10c95-426">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10c95-427">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10c95-427">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10c95-428">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10c95-428">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10c95-429">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-429">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10c95-430">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10c95-430">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10c95-431">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10c95-431">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10c95-432">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10c95-432">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10c95-433">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-433">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-434">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-434">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10c95-435">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10c95-435">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10c95-436">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10c95-436">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10c95-437">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-437">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10c95-438">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-438">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10c95-439">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-439">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10c95-440">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-440">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10c95-441">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10c95-441">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10c95-442">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-442">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10c95-443">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-443">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-444">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-444">Use a valid IP address.</span></span>
   * <span data-ttu-id="10c95-445">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-445">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10c95-446">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10c95-446">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10c95-447">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10c95-447">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10c95-448">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10c95-448">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10c95-449">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10c95-449">In Visual Studio:</span></span>
     * <span data-ttu-id="10c95-450">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10c95-450">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10c95-451">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10c95-451">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10c95-452">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10c95-452">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10c95-453">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10c95-453">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10c95-454">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10c95-454">Open the app's project file.</span></span>
     * <span data-ttu-id="10c95-455">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10c95-455">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10c95-456">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10c95-456">In the following example:</span></span>

   * <span data-ttu-id="10c95-457">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10c95-457">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10c95-458">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10c95-458">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10c95-459">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-459">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10c95-460">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-460">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10c95-461">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10c95-461">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10c95-462">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10c95-462">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10c95-463">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10c95-463">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10c95-464">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-464">Run the app.</span></span>

   <span data-ttu-id="10c95-465">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-465">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10c95-466">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-466">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10c95-467">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10c95-467">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10c95-468">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-468">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10c95-469">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-469">A development certificate is used in this example.</span></span> <span data-ttu-id="10c95-470">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10c95-470">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10c95-472">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10c95-472">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10c95-473">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-473">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10c95-474">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10c95-474">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10c95-475">其他资源</span><span class="sxs-lookup"><span data-stu-id="10c95-475">Additional resources</span></span>

* [<span data-ttu-id="10c95-476">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-476">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10c95-477">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10c95-477">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10c95-478">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10c95-478">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10c95-479">主机</span><span class="sxs-lookup"><span data-stu-id="10c95-479">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="10c95-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10c95-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10c95-481">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-481">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10c95-482">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10c95-482">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-483">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10c95-483">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10c95-484">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-484">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10c95-485">端口共享</span><span class="sxs-lookup"><span data-stu-id="10c95-485">Port sharing</span></span>
* <span data-ttu-id="10c95-486">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10c95-486">HTTPS with SNI</span></span>
* <span data-ttu-id="10c95-487">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-487">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10c95-488">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10c95-488">Direct file transmission</span></span>
* <span data-ttu-id="10c95-489">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10c95-489">Response caching</span></span>
* <span data-ttu-id="10c95-490">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-490">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10c95-491">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10c95-491">Supported Windows versions:</span></span>

* <span data-ttu-id="10c95-492">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-492">Windows 7 or later</span></span>
* <span data-ttu-id="10c95-493">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-493">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10c95-494">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10c95-494">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10c95-495">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-495">When to use HTTP.sys</span></span>

<span data-ttu-id="10c95-496">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10c95-496">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10c95-497">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10c95-497">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10c95-499">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10c95-499">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10c95-501">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-501">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10c95-502">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10c95-502">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10c95-503">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10c95-503">HTTP/2 support</span></span>

<span data-ttu-id="10c95-504">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10c95-504">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10c95-505">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-505">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10c95-506">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10c95-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10c95-507">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10c95-507">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10c95-508">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="10c95-508">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="10c95-509">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c95-509">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10c95-510">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10c95-510">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10c95-511">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-511">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10c95-512">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-512">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10c95-513">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-513">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10c95-514">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-514">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10c95-515">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-515">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10c95-516">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10c95-516">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10c95-517">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-517">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10c95-518">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-518">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10c95-519">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="10c95-519">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="10c95-520">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="10c95-520">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="10c95-521">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10c95-521">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10c95-522">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-522">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="10c95-523">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-523">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10c95-524">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10c95-524">**HTTP.sys options**</span></span>

| <span data-ttu-id="10c95-525">Property</span><span class="sxs-lookup"><span data-stu-id="10c95-525">Property</span></span> | <span data-ttu-id="10c95-526">描述</span><span class="sxs-lookup"><span data-stu-id="10c95-526">Description</span></span> | <span data-ttu-id="10c95-527">默认</span><span class="sxs-lookup"><span data-stu-id="10c95-527">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="10c95-528">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="10c95-528">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="10c95-529">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10c95-529">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="10c95-530">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10c95-530">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10c95-531">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10c95-531">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10c95-532">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10c95-532">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10c95-533">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10c95-533">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10c95-534">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-534">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10c95-535">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10c95-535">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="10c95-536">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="10c95-536">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="10c95-537">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-537">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10c95-538">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-538">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10c95-539">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-539">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10c95-540">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10c95-540">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10c95-541">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10c95-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10c95-542">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10c95-542">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10c95-543">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10c95-543">Use `-1` for infinite.</span></span> <span data-ttu-id="10c95-544">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-544">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10c95-545">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10c95-545">(machine-wide</span></span><br><span data-ttu-id="10c95-546">设置）</span><span class="sxs-lookup"><span data-stu-id="10c95-546">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10c95-547">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10c95-547">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10c95-548">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10c95-548">30000000 bytes</span></span><br><span data-ttu-id="10c95-549">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10c95-549">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10c95-550">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10c95-550">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10c95-551">1000</span><span class="sxs-lookup"><span data-stu-id="10c95-551">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10c95-552">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10c95-552">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10c95-553">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10c95-553">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10c95-554">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-554">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10c95-555">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-555">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10c95-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10c95-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10c95-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10c95-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10c95-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10c95-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10c95-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10c95-562">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10c95-562">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10c95-563">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10c95-563">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10c95-564">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-564">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10c95-565">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10c95-565">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10c95-566">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10c95-566">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10c95-567">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-567">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10c95-568">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-568">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10c95-569">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10c95-569">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10c95-570">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10c95-570">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10c95-571">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-571">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10c95-572">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10c95-572">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10c95-573">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10c95-573">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-574">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10c95-574">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10c95-575">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10c95-575">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10c95-577">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10c95-577">Configure Windows Server</span></span>

1. <span data-ttu-id="10c95-578">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10c95-578">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10c95-579">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-579">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-580">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-580">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10c95-581">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-581">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-582">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-582">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10c95-583">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-583">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10c95-584">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10c95-584">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10c95-585">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10c95-585">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10c95-586">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10c95-586">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10c95-587">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-587">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10c95-588">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10c95-588">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10c95-589">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10c95-589">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10c95-590">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10c95-590">Install the required .NET Framework.</span></span> <span data-ttu-id="10c95-591">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-591">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10c95-592">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10c95-592">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10c95-593">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10c95-593">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10c95-594">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-594">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10c95-595">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10c95-595">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10c95-596">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10c95-596">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10c95-597">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10c95-597">`urls` command-line argument</span></span>
   * <span data-ttu-id="10c95-598">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10c95-598">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10c95-599">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-599">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="10c95-600">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10c95-600">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10c95-601">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-601">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10c95-602">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10c95-602">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10c95-603">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10c95-603">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10c95-604">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10c95-604">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10c95-605">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10c95-605">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10c95-606">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-606">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10c95-607">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-607">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10c95-608">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10c95-608">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10c95-609">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10c95-609">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10c95-610">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10c95-610">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10c95-611">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10c95-611">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10c95-612">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-612">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10c95-613">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10c95-613">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10c95-614">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10c95-614">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10c95-615">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10c95-615">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10c95-616">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-616">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-617">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-617">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10c95-618">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10c95-618">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10c95-619">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10c95-619">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10c95-620">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-620">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10c95-621">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-621">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10c95-622">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-622">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10c95-623">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-623">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10c95-624">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10c95-624">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10c95-625">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-625">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10c95-626">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-626">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-627">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-627">Use a valid IP address.</span></span>
   * <span data-ttu-id="10c95-628">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-628">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10c95-629">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10c95-629">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10c95-630">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10c95-630">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10c95-631">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10c95-631">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10c95-632">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10c95-632">In Visual Studio:</span></span>
     * <span data-ttu-id="10c95-633">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10c95-633">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10c95-634">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10c95-634">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10c95-635">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10c95-635">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10c95-636">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10c95-636">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10c95-637">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10c95-637">Open the app's project file.</span></span>
     * <span data-ttu-id="10c95-638">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10c95-638">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10c95-639">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10c95-639">In the following example:</span></span>

   * <span data-ttu-id="10c95-640">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10c95-640">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10c95-641">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10c95-641">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10c95-642">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-642">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10c95-643">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-643">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10c95-644">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10c95-644">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10c95-645">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10c95-645">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10c95-646">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10c95-646">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10c95-647">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-647">Run the app.</span></span>

   <span data-ttu-id="10c95-648">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-648">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10c95-649">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-649">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10c95-650">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10c95-650">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10c95-651">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-651">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10c95-652">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-652">A development certificate is used in this example.</span></span> <span data-ttu-id="10c95-653">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10c95-653">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10c95-655">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10c95-655">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10c95-656">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-656">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10c95-657">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10c95-657">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10c95-658">其他资源</span><span class="sxs-lookup"><span data-stu-id="10c95-658">Additional resources</span></span>

* [<span data-ttu-id="10c95-659">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-659">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10c95-660">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10c95-660">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10c95-661">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10c95-661">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10c95-662">主机</span><span class="sxs-lookup"><span data-stu-id="10c95-662">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="10c95-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10c95-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10c95-664">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-664">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10c95-665">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10c95-665">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-666">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10c95-666">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10c95-667">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-667">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10c95-668">端口共享</span><span class="sxs-lookup"><span data-stu-id="10c95-668">Port sharing</span></span>
* <span data-ttu-id="10c95-669">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10c95-669">HTTPS with SNI</span></span>
* <span data-ttu-id="10c95-670">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-670">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10c95-671">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10c95-671">Direct file transmission</span></span>
* <span data-ttu-id="10c95-672">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10c95-672">Response caching</span></span>
* <span data-ttu-id="10c95-673">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10c95-673">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10c95-674">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10c95-674">Supported Windows versions:</span></span>

* <span data-ttu-id="10c95-675">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-675">Windows 7 or later</span></span>
* <span data-ttu-id="10c95-676">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-676">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10c95-677">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10c95-677">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10c95-678">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-678">When to use HTTP.sys</span></span>

<span data-ttu-id="10c95-679">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10c95-679">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10c95-680">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10c95-680">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10c95-682">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10c95-682">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10c95-684">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-684">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10c95-685">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10c95-685">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10c95-686">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10c95-686">HTTP/2 support</span></span>

<span data-ttu-id="10c95-687">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10c95-687">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10c95-688">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10c95-688">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10c95-689">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10c95-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10c95-690">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10c95-690">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10c95-691">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="10c95-691">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="10c95-692">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10c95-692">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10c95-693">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10c95-693">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10c95-694">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10c95-694">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10c95-695">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-695">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10c95-696">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-696">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10c95-697">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-697">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10c95-698">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10c95-698">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10c95-699">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10c95-699">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10c95-700">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-700">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10c95-701">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10c95-701">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10c95-702">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="10c95-702">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="10c95-703">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="10c95-703">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="10c95-704">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10c95-704">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10c95-705">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-705">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="10c95-706">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-706">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10c95-707">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10c95-707">**HTTP.sys options**</span></span>

| <span data-ttu-id="10c95-708">Property</span><span class="sxs-lookup"><span data-stu-id="10c95-708">Property</span></span> | <span data-ttu-id="10c95-709">描述</span><span class="sxs-lookup"><span data-stu-id="10c95-709">Description</span></span> | <span data-ttu-id="10c95-710">默认</span><span class="sxs-lookup"><span data-stu-id="10c95-710">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="10c95-711">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="10c95-711">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="10c95-712">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10c95-712">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="10c95-713">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10c95-713">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10c95-714">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10c95-714">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10c95-715">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10c95-715">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10c95-716">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10c95-716">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10c95-717">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-717">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10c95-718">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10c95-718">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="10c95-719">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="10c95-719">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="10c95-720">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-720">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10c95-721">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-721">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10c95-722">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10c95-722">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10c95-723">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10c95-723">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10c95-724">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10c95-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10c95-725">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10c95-725">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10c95-726">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10c95-726">Use `-1` for infinite.</span></span> <span data-ttu-id="10c95-727">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-727">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10c95-728">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10c95-728">(machine-wide</span></span><br><span data-ttu-id="10c95-729">设置）</span><span class="sxs-lookup"><span data-stu-id="10c95-729">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10c95-730">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10c95-730">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10c95-731">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10c95-731">30000000 bytes</span></span><br><span data-ttu-id="10c95-732">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10c95-732">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10c95-733">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10c95-733">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10c95-734">1000</span><span class="sxs-lookup"><span data-stu-id="10c95-734">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10c95-735">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10c95-735">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10c95-736">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10c95-736">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10c95-737">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-737">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10c95-738">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10c95-738">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10c95-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10c95-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10c95-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10c95-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10c95-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10c95-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10c95-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10c95-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10c95-745">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10c95-745">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10c95-746">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10c95-746">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10c95-747">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10c95-747">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10c95-748">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10c95-748">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10c95-749">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10c95-749">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10c95-750">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-750">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10c95-751">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-751">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10c95-752">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10c95-752">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10c95-753">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10c95-753">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10c95-754">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10c95-754">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10c95-755">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10c95-755">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10c95-756">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10c95-756">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10c95-757">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10c95-757">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10c95-758">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10c95-758">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10c95-760">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10c95-760">Configure Windows Server</span></span>

1. <span data-ttu-id="10c95-761">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10c95-761">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10c95-762">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-762">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-763">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-763">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10c95-764">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10c95-764">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10c95-765">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-765">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10c95-766">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-766">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10c95-767">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10c95-767">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10c95-768">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10c95-768">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10c95-769">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10c95-769">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10c95-770">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-770">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10c95-771">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10c95-771">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10c95-772">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10c95-772">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10c95-773">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10c95-773">Install the required .NET Framework.</span></span> <span data-ttu-id="10c95-774">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10c95-774">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10c95-775">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10c95-775">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10c95-776">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10c95-776">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10c95-777">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-777">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10c95-778">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10c95-778">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10c95-779">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10c95-779">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10c95-780">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10c95-780">`urls` command-line argument</span></span>
   * <span data-ttu-id="10c95-781">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10c95-781">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10c95-782">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-782">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="10c95-783">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10c95-783">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10c95-784">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10c95-784">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10c95-785">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10c95-785">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10c95-786">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10c95-786">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10c95-787">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10c95-787">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10c95-788">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10c95-788">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10c95-789">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-789">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10c95-790">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10c95-790">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10c95-791">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10c95-791">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10c95-792">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10c95-792">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10c95-793">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10c95-793">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10c95-794">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10c95-794">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10c95-795">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-795">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10c95-796">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10c95-796">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10c95-797">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10c95-797">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10c95-798">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10c95-798">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10c95-799">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-799">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-800">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-800">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10c95-801">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10c95-801">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10c95-802">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10c95-802">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10c95-803">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10c95-803">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10c95-804">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-804">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10c95-805">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-805">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10c95-806">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-806">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10c95-807">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10c95-807">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10c95-808">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-808">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10c95-809">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10c95-809">Don't use a wildcard binding.</span></span> <span data-ttu-id="10c95-810">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10c95-810">Use a valid IP address.</span></span>
   * <span data-ttu-id="10c95-811">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10c95-811">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10c95-812">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10c95-812">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10c95-813">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10c95-813">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10c95-814">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10c95-814">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10c95-815">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10c95-815">In Visual Studio:</span></span>
     * <span data-ttu-id="10c95-816">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10c95-816">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10c95-817">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10c95-817">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10c95-818">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10c95-818">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10c95-819">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10c95-819">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10c95-820">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10c95-820">Open the app's project file.</span></span>
     * <span data-ttu-id="10c95-821">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10c95-821">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10c95-822">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10c95-822">In the following example:</span></span>

   * <span data-ttu-id="10c95-823">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10c95-823">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10c95-824">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10c95-824">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10c95-825">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10c95-825">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10c95-826">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10c95-826">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10c95-827">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10c95-827">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10c95-828">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10c95-828">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10c95-829">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10c95-829">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10c95-830">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-830">Run the app.</span></span>

   <span data-ttu-id="10c95-831">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-831">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10c95-832">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10c95-832">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10c95-833">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10c95-833">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10c95-834">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10c95-834">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10c95-835">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10c95-835">A development certificate is used in this example.</span></span> <span data-ttu-id="10c95-836">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10c95-836">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10c95-838">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10c95-838">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10c95-839">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10c95-839">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10c95-840">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10c95-840">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10c95-841">其他资源</span><span class="sxs-lookup"><span data-stu-id="10c95-841">Additional resources</span></span>

* [<span data-ttu-id="10c95-842">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10c95-842">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10c95-843">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10c95-843">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10c95-844">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10c95-844">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10c95-845">主机</span><span class="sxs-lookup"><span data-stu-id="10c95-845">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
