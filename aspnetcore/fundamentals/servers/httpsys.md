---
title: ASP.NET Core 中的 HTTP.sys Web 服务器实现
author: rick-anderson
description: 了解 Windows 上适用于 ASP.NET Core 的 Web 服务器 HTTP.sys。 HTTP.sys 构建于 HTTP.sys 内核模式驱动程序之上，是 Kestrel 的一种替代选择，可用来直接连接到 Internet，而无需使用 IIS。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
ms.openlocfilehash: e5346c1e58127747d777b5040fe7bc7d99b9a489
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722866"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="4b058-104">ASP.NET Core 中的 HTTP.sys Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="4b058-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="4b058-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="4b058-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="4b058-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="4b058-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="4b058-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b058-108">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="4b058-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-109">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="4b058-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="4b058-110">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="4b058-111">端口共享</span><span class="sxs-lookup"><span data-stu-id="4b058-111">Port sharing</span></span>
* <span data-ttu-id="4b058-112">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="4b058-112">HTTPS with SNI</span></span>
* <span data-ttu-id="4b058-113">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="4b058-114">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="4b058-114">Direct file transmission</span></span>
* <span data-ttu-id="4b058-115">响应缓存</span><span class="sxs-lookup"><span data-stu-id="4b058-115">Response caching</span></span>
* <span data-ttu-id="4b058-116">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="4b058-117">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="4b058-117">Supported Windows versions:</span></span>

* <span data-ttu-id="4b058-118">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-118">Windows 7 or later</span></span>
* <span data-ttu-id="4b058-119">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4b058-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4b058-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="4b058-121">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-121">When to use HTTP.sys</span></span>

<span data-ttu-id="4b058-122">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="4b058-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="4b058-123">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="4b058-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="4b058-125">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4b058-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="4b058-127">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="4b058-128">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="4b058-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4b058-129">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="4b058-129">HTTP/2 support</span></span>

<span data-ttu-id="4b058-130">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="4b058-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="4b058-131">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="4b058-132">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="4b058-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="4b058-133">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4b058-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4b058-134">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="4b058-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="4b058-135">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="4b058-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4b058-136">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4b058-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="4b058-137">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="4b058-138">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="4b058-139">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="4b058-140">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="4b058-141">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="4b058-142">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="4b058-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="4b058-143">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="4b058-144">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="4b058-145">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="4b058-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="4b058-146">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="4b058-147">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="4b058-148">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="4b058-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="4b058-149">Property</span><span class="sxs-lookup"><span data-stu-id="4b058-149">Property</span></span> | <span data-ttu-id="4b058-150">描述</span><span class="sxs-lookup"><span data-stu-id="4b058-150">Description</span></span> | <span data-ttu-id="4b058-151">默认</span><span class="sxs-lookup"><span data-stu-id="4b058-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b058-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="4b058-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="4b058-153">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="4b058-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="4b058-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="4b058-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="4b058-155">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="4b058-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="4b058-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="4b058-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="4b058-157">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4b058-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="4b058-158">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="4b058-159">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="4b058-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="4b058-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="4b058-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="4b058-161">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="4b058-162">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="4b058-163">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="4b058-164">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="4b058-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="4b058-165">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="4b058-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="4b058-166">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="4b058-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="4b058-167">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="4b058-167">Use `-1` for infinite.</span></span> <span data-ttu-id="4b058-168">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="4b058-169">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="4b058-169">(machine-wide</span></span><br><span data-ttu-id="4b058-170">设置）</span><span class="sxs-lookup"><span data-stu-id="4b058-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="4b058-171">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="4b058-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="4b058-172">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="4b058-172">30000000 bytes</span></span><br><span data-ttu-id="4b058-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="4b058-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="4b058-174">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="4b058-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="4b058-175">1000</span><span class="sxs-lookup"><span data-stu-id="4b058-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="4b058-176">这指示服务器是否负责创建和配置请求队列，或是否应附加到现有队列。</span><span class="sxs-lookup"><span data-stu-id="4b058-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="4b058-177">附加到现有队列时，大多数现有配置选项不适用。</span><span class="sxs-lookup"><span data-stu-id="4b058-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="4b058-178">HTTP.sys 请求队列的名称。</span><span class="sxs-lookup"><span data-stu-id="4b058-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="4b058-179">`null`（匿名队列）</span><span class="sxs-lookup"><span data-stu-id="4b058-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="4b058-180">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="4b058-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="4b058-181">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="4b058-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="4b058-182">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="4b058-183">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="4b058-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="4b058-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="4b058-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="4b058-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="4b058-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="4b058-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="4b058-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="4b058-190">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="4b058-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="4b058-191">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="4b058-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="4b058-192">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="4b058-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="4b058-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="4b058-194">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="4b058-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="4b058-195">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="4b058-196">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="4b058-197">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="4b058-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="4b058-198">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="4b058-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="4b058-199">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="4b058-200">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="4b058-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="4b058-201">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="4b058-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-202">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="4b058-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="4b058-203">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="4b058-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="4b058-205">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="4b058-205">Configure Windows Server</span></span>

1. <span data-ttu-id="4b058-206">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="4b058-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="4b058-207">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-208">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="4b058-209">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-210">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="4b058-211">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="4b058-212">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="4b058-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="4b058-213">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="4b058-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="4b058-214">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="4b058-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="4b058-215">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="4b058-216">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="4b058-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="4b058-217">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="4b058-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="4b058-218">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="4b058-218">Install the required .NET Framework.</span></span> <span data-ttu-id="4b058-219">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="4b058-220">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="4b058-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="4b058-221">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="4b058-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="4b058-222">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="4b058-223">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="4b058-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4b058-224">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="4b058-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="4b058-225">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="4b058-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="4b058-226">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="4b058-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="4b058-227">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="4b058-228">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="4b058-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="4b058-229">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="4b058-230">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="4b058-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="4b058-231">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="4b058-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="4b058-232">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="4b058-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4b058-233">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="4b058-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="4b058-234">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4b058-235">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="4b058-236">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="4b058-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4b058-237">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="4b058-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="4b058-238">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="4b058-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="4b058-239">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="4b058-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="4b058-240">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="4b058-241">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="4b058-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="4b058-242">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="4b058-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="4b058-243">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="4b058-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="4b058-244">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-245">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="4b058-246">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="4b058-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="4b058-247">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="4b058-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="4b058-248">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="4b058-249">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="4b058-250">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="4b058-251">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="4b058-252">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="4b058-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="4b058-253">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="4b058-254">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-255">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="4b058-256">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="4b058-257">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="4b058-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="4b058-258">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="4b058-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="4b058-259">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="4b058-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="4b058-260">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="4b058-260">In Visual Studio:</span></span>
     * <span data-ttu-id="4b058-261">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="4b058-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="4b058-262">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="4b058-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="4b058-263">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="4b058-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="4b058-264">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="4b058-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="4b058-265">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="4b058-265">Open the app's project file.</span></span>
     * <span data-ttu-id="4b058-266">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="4b058-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="4b058-267">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="4b058-267">In the following example:</span></span>

   * <span data-ttu-id="4b058-268">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="4b058-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="4b058-269">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="4b058-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="4b058-270">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="4b058-271">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="4b058-272">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="4b058-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="4b058-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="4b058-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="4b058-274">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="4b058-274">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="4b058-275">运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-275">Run the app.</span></span>

   <span data-ttu-id="4b058-276">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="4b058-277">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="4b058-278">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="4b058-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="4b058-279">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="4b058-280">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-280">A development certificate is used in this example.</span></span> <span data-ttu-id="4b058-281">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="4b058-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4b058-283">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="4b058-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4b058-284">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="4b058-285">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="4b058-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b058-286">其他资源</span><span class="sxs-lookup"><span data-stu-id="4b058-286">Additional resources</span></span>

* [<span data-ttu-id="4b058-287">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-287">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="4b058-288">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="4b058-288">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="4b058-289">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="4b058-289">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="4b058-290">主机</span><span class="sxs-lookup"><span data-stu-id="4b058-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="4b058-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="4b058-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="4b058-292">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b058-293">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="4b058-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-294">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="4b058-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="4b058-295">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="4b058-296">端口共享</span><span class="sxs-lookup"><span data-stu-id="4b058-296">Port sharing</span></span>
* <span data-ttu-id="4b058-297">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="4b058-297">HTTPS with SNI</span></span>
* <span data-ttu-id="4b058-298">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="4b058-299">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="4b058-299">Direct file transmission</span></span>
* <span data-ttu-id="4b058-300">响应缓存</span><span class="sxs-lookup"><span data-stu-id="4b058-300">Response caching</span></span>
* <span data-ttu-id="4b058-301">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="4b058-302">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="4b058-302">Supported Windows versions:</span></span>

* <span data-ttu-id="4b058-303">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-303">Windows 7 or later</span></span>
* <span data-ttu-id="4b058-304">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4b058-305">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4b058-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="4b058-306">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-306">When to use HTTP.sys</span></span>

<span data-ttu-id="4b058-307">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="4b058-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="4b058-308">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="4b058-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="4b058-310">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4b058-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="4b058-312">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="4b058-313">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="4b058-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4b058-314">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="4b058-314">HTTP/2 support</span></span>

<span data-ttu-id="4b058-315">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="4b058-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="4b058-316">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="4b058-317">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="4b058-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="4b058-318">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4b058-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4b058-319">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="4b058-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="4b058-320">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="4b058-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4b058-321">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4b058-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="4b058-322">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="4b058-323">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="4b058-324">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="4b058-325">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="4b058-326">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="4b058-327">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="4b058-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="4b058-328">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="4b058-329">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="4b058-330">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="4b058-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="4b058-331">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="4b058-332">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="4b058-333">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="4b058-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="4b058-334">Property</span><span class="sxs-lookup"><span data-stu-id="4b058-334">Property</span></span> | <span data-ttu-id="4b058-335">描述</span><span class="sxs-lookup"><span data-stu-id="4b058-335">Description</span></span> | <span data-ttu-id="4b058-336">默认</span><span class="sxs-lookup"><span data-stu-id="4b058-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b058-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="4b058-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="4b058-338">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="4b058-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="4b058-339">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="4b058-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="4b058-340">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="4b058-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="4b058-341">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="4b058-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="4b058-342">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4b058-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="4b058-343">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="4b058-344">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="4b058-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="4b058-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="4b058-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="4b058-346">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="4b058-347">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="4b058-348">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="4b058-349">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="4b058-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="4b058-350">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="4b058-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="4b058-351">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="4b058-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="4b058-352">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="4b058-352">Use `-1` for infinite.</span></span> <span data-ttu-id="4b058-353">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="4b058-354">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="4b058-354">(machine-wide</span></span><br><span data-ttu-id="4b058-355">设置）</span><span class="sxs-lookup"><span data-stu-id="4b058-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="4b058-356">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="4b058-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="4b058-357">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="4b058-357">30000000 bytes</span></span><br><span data-ttu-id="4b058-358">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="4b058-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="4b058-359">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="4b058-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="4b058-360">1000</span><span class="sxs-lookup"><span data-stu-id="4b058-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="4b058-361">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="4b058-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="4b058-362">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="4b058-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="4b058-363">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="4b058-364">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="4b058-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="4b058-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="4b058-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="4b058-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="4b058-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="4b058-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="4b058-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="4b058-371">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="4b058-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="4b058-372">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="4b058-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="4b058-373">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="4b058-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="4b058-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="4b058-375">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="4b058-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="4b058-376">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="4b058-377">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="4b058-378">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="4b058-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="4b058-379">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="4b058-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="4b058-380">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="4b058-381">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="4b058-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="4b058-382">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="4b058-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-383">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="4b058-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="4b058-384">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="4b058-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="4b058-386">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="4b058-386">Configure Windows Server</span></span>

1. <span data-ttu-id="4b058-387">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="4b058-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="4b058-388">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-389">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="4b058-390">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-391">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="4b058-392">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="4b058-393">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="4b058-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="4b058-394">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="4b058-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="4b058-395">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="4b058-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="4b058-396">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-396">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="4b058-397">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="4b058-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="4b058-398">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="4b058-398">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="4b058-399">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="4b058-399">Install the required .NET Framework.</span></span> <span data-ttu-id="4b058-400">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="4b058-401">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="4b058-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="4b058-402">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="4b058-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="4b058-403">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="4b058-404">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="4b058-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4b058-405">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="4b058-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="4b058-406">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="4b058-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="4b058-407">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="4b058-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="4b058-408">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="4b058-409">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="4b058-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="4b058-410">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="4b058-411">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="4b058-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="4b058-412">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="4b058-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="4b058-413">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="4b058-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4b058-414">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="4b058-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="4b058-415">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4b058-416">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="4b058-417">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="4b058-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4b058-418">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="4b058-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="4b058-419">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="4b058-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="4b058-420">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="4b058-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="4b058-421">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="4b058-422">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="4b058-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="4b058-423">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="4b058-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="4b058-424">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="4b058-424">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="4b058-425">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-426">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="4b058-427">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="4b058-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="4b058-428">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="4b058-428">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="4b058-429">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="4b058-430">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="4b058-431">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="4b058-432">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="4b058-433">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="4b058-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="4b058-434">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-434">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="4b058-435">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-436">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="4b058-437">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-437">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="4b058-438">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="4b058-438">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="4b058-439">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="4b058-439">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="4b058-440">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="4b058-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="4b058-441">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="4b058-441">In Visual Studio:</span></span>
     * <span data-ttu-id="4b058-442">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="4b058-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="4b058-443">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="4b058-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="4b058-444">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="4b058-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="4b058-445">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="4b058-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="4b058-446">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="4b058-446">Open the app's project file.</span></span>
     * <span data-ttu-id="4b058-447">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="4b058-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="4b058-448">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="4b058-448">In the following example:</span></span>

   * <span data-ttu-id="4b058-449">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="4b058-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="4b058-450">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="4b058-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="4b058-451">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="4b058-452">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="4b058-453">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="4b058-453">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="4b058-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="4b058-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="4b058-455">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="4b058-455">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="4b058-456">运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-456">Run the app.</span></span>

   <span data-ttu-id="4b058-457">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="4b058-458">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="4b058-459">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="4b058-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="4b058-460">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="4b058-461">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-461">A development certificate is used in this example.</span></span> <span data-ttu-id="4b058-462">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="4b058-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4b058-464">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="4b058-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4b058-465">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="4b058-466">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="4b058-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b058-467">其他资源</span><span class="sxs-lookup"><span data-stu-id="4b058-467">Additional resources</span></span>

* [<span data-ttu-id="4b058-468">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-468">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="4b058-469">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="4b058-469">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="4b058-470">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="4b058-470">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="4b058-471">主机</span><span class="sxs-lookup"><span data-stu-id="4b058-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="4b058-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="4b058-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="4b058-473">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b058-474">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="4b058-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-475">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="4b058-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="4b058-476">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="4b058-477">端口共享</span><span class="sxs-lookup"><span data-stu-id="4b058-477">Port sharing</span></span>
* <span data-ttu-id="4b058-478">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="4b058-478">HTTPS with SNI</span></span>
* <span data-ttu-id="4b058-479">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="4b058-480">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="4b058-480">Direct file transmission</span></span>
* <span data-ttu-id="4b058-481">响应缓存</span><span class="sxs-lookup"><span data-stu-id="4b058-481">Response caching</span></span>
* <span data-ttu-id="4b058-482">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="4b058-483">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="4b058-483">Supported Windows versions:</span></span>

* <span data-ttu-id="4b058-484">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-484">Windows 7 or later</span></span>
* <span data-ttu-id="4b058-485">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4b058-486">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4b058-486">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="4b058-487">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-487">When to use HTTP.sys</span></span>

<span data-ttu-id="4b058-488">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="4b058-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="4b058-489">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="4b058-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="4b058-491">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4b058-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="4b058-493">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="4b058-494">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="4b058-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4b058-495">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="4b058-495">HTTP/2 support</span></span>

<span data-ttu-id="4b058-496">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="4b058-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="4b058-497">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="4b058-498">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="4b058-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="4b058-499">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4b058-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4b058-500">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="4b058-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="4b058-501">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="4b058-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4b058-502">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4b058-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="4b058-503">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="4b058-504">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="4b058-505">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="4b058-506">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="4b058-507">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="4b058-508">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="4b058-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="4b058-509">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="4b058-510">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="4b058-511">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="4b058-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="4b058-512">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="4b058-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="4b058-513">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="4b058-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="4b058-514">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="4b058-515">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="4b058-516">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="4b058-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="4b058-517">Property</span><span class="sxs-lookup"><span data-stu-id="4b058-517">Property</span></span> | <span data-ttu-id="4b058-518">描述</span><span class="sxs-lookup"><span data-stu-id="4b058-518">Description</span></span> | <span data-ttu-id="4b058-519">默认</span><span class="sxs-lookup"><span data-stu-id="4b058-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b058-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="4b058-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="4b058-521">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="4b058-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="4b058-522">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="4b058-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="4b058-523">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="4b058-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="4b058-524">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="4b058-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="4b058-525">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4b058-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="4b058-526">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="4b058-527">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="4b058-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="4b058-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="4b058-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="4b058-529">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="4b058-530">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="4b058-531">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="4b058-532">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="4b058-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="4b058-533">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="4b058-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="4b058-534">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="4b058-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="4b058-535">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="4b058-535">Use `-1` for infinite.</span></span> <span data-ttu-id="4b058-536">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="4b058-537">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="4b058-537">(machine-wide</span></span><br><span data-ttu-id="4b058-538">设置）</span><span class="sxs-lookup"><span data-stu-id="4b058-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="4b058-539">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="4b058-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="4b058-540">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="4b058-540">30000000 bytes</span></span><br><span data-ttu-id="4b058-541">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="4b058-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="4b058-542">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="4b058-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="4b058-543">1000</span><span class="sxs-lookup"><span data-stu-id="4b058-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="4b058-544">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="4b058-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="4b058-545">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="4b058-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="4b058-546">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="4b058-547">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="4b058-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="4b058-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="4b058-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="4b058-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="4b058-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="4b058-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="4b058-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="4b058-554">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="4b058-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="4b058-555">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="4b058-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="4b058-556">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="4b058-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="4b058-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="4b058-558">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="4b058-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="4b058-559">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="4b058-560">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="4b058-561">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="4b058-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="4b058-562">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="4b058-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="4b058-563">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="4b058-564">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="4b058-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="4b058-565">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="4b058-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-566">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="4b058-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="4b058-567">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="4b058-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="4b058-569">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="4b058-569">Configure Windows Server</span></span>

1. <span data-ttu-id="4b058-570">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="4b058-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="4b058-571">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-572">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="4b058-573">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-574">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="4b058-575">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="4b058-576">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="4b058-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="4b058-577">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="4b058-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="4b058-578">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="4b058-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="4b058-579">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-579">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="4b058-580">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="4b058-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="4b058-581">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="4b058-581">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="4b058-582">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="4b058-582">Install the required .NET Framework.</span></span> <span data-ttu-id="4b058-583">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="4b058-584">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="4b058-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="4b058-585">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="4b058-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="4b058-586">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="4b058-587">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="4b058-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4b058-588">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="4b058-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="4b058-589">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="4b058-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="4b058-590">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="4b058-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="4b058-591">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="4b058-592">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="4b058-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="4b058-593">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="4b058-594">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="4b058-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="4b058-595">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="4b058-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="4b058-596">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="4b058-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4b058-597">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="4b058-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="4b058-598">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4b058-599">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="4b058-600">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="4b058-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4b058-601">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="4b058-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="4b058-602">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="4b058-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="4b058-603">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="4b058-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="4b058-604">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="4b058-605">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="4b058-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="4b058-606">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="4b058-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="4b058-607">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="4b058-607">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="4b058-608">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-609">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="4b058-610">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="4b058-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="4b058-611">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="4b058-611">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="4b058-612">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="4b058-613">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="4b058-614">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="4b058-615">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="4b058-616">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="4b058-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="4b058-617">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-617">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="4b058-618">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-619">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="4b058-620">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-620">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="4b058-621">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="4b058-621">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="4b058-622">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="4b058-622">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="4b058-623">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="4b058-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="4b058-624">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="4b058-624">In Visual Studio:</span></span>
     * <span data-ttu-id="4b058-625">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="4b058-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="4b058-626">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="4b058-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="4b058-627">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="4b058-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="4b058-628">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="4b058-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="4b058-629">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="4b058-629">Open the app's project file.</span></span>
     * <span data-ttu-id="4b058-630">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="4b058-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="4b058-631">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="4b058-631">In the following example:</span></span>

   * <span data-ttu-id="4b058-632">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="4b058-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="4b058-633">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="4b058-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="4b058-634">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="4b058-635">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="4b058-636">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="4b058-636">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="4b058-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="4b058-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="4b058-638">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="4b058-638">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="4b058-639">运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-639">Run the app.</span></span>

   <span data-ttu-id="4b058-640">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="4b058-641">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="4b058-642">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="4b058-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="4b058-643">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="4b058-644">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-644">A development certificate is used in this example.</span></span> <span data-ttu-id="4b058-645">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="4b058-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4b058-647">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="4b058-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4b058-648">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="4b058-649">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="4b058-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b058-650">其他资源</span><span class="sxs-lookup"><span data-stu-id="4b058-650">Additional resources</span></span>

* [<span data-ttu-id="4b058-651">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-651">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="4b058-652">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="4b058-652">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="4b058-653">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="4b058-653">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="4b058-654">主机</span><span class="sxs-lookup"><span data-stu-id="4b058-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="4b058-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="4b058-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="4b058-656">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4b058-657">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="4b058-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-658">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="4b058-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="4b058-659">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="4b058-660">端口共享</span><span class="sxs-lookup"><span data-stu-id="4b058-660">Port sharing</span></span>
* <span data-ttu-id="4b058-661">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="4b058-661">HTTPS with SNI</span></span>
* <span data-ttu-id="4b058-662">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="4b058-663">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="4b058-663">Direct file transmission</span></span>
* <span data-ttu-id="4b058-664">响应缓存</span><span class="sxs-lookup"><span data-stu-id="4b058-664">Response caching</span></span>
* <span data-ttu-id="4b058-665">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="4b058-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="4b058-666">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="4b058-666">Supported Windows versions:</span></span>

* <span data-ttu-id="4b058-667">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-667">Windows 7 or later</span></span>
* <span data-ttu-id="4b058-668">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="4b058-669">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4b058-669">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="4b058-670">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-670">When to use HTTP.sys</span></span>

<span data-ttu-id="4b058-671">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="4b058-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="4b058-672">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="4b058-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="4b058-674">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="4b058-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="4b058-676">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="4b058-677">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="4b058-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="4b058-678">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="4b058-678">HTTP/2 support</span></span>

<span data-ttu-id="4b058-679">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="4b058-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="4b058-680">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="4b058-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="4b058-681">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="4b058-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="4b058-682">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="4b058-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="4b058-683">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="4b058-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="4b058-684">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="4b058-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="4b058-685">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="4b058-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="4b058-686">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="4b058-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="4b058-687">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="4b058-688">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="4b058-689">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="4b058-690">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b058-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="4b058-691">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="4b058-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="4b058-692">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="4b058-693">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4b058-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="4b058-694">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="4b058-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="4b058-695">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="4b058-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="4b058-696">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="4b058-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="4b058-697">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="4b058-698">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="4b058-699">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="4b058-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="4b058-700">Property</span><span class="sxs-lookup"><span data-stu-id="4b058-700">Property</span></span> | <span data-ttu-id="4b058-701">描述</span><span class="sxs-lookup"><span data-stu-id="4b058-701">Description</span></span> | <span data-ttu-id="4b058-702">默认</span><span class="sxs-lookup"><span data-stu-id="4b058-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b058-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="4b058-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="4b058-704">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="4b058-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="4b058-705">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="4b058-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="4b058-706">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="4b058-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="4b058-707">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="4b058-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="4b058-708">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4b058-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="4b058-709">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="4b058-710">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="4b058-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="4b058-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="4b058-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="4b058-712">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="4b058-713">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="4b058-714">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="4b058-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="4b058-715">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="4b058-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="4b058-716">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="4b058-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="4b058-717">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="4b058-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="4b058-718">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="4b058-718">Use `-1` for infinite.</span></span> <span data-ttu-id="4b058-719">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="4b058-720">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="4b058-720">(machine-wide</span></span><br><span data-ttu-id="4b058-721">设置）</span><span class="sxs-lookup"><span data-stu-id="4b058-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="4b058-722">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="4b058-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="4b058-723">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="4b058-723">30000000 bytes</span></span><br><span data-ttu-id="4b058-724">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="4b058-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="4b058-725">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="4b058-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="4b058-726">1000</span><span class="sxs-lookup"><span data-stu-id="4b058-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="4b058-727">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="4b058-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="4b058-728">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="4b058-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="4b058-729">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="4b058-730">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="4b058-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="4b058-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="4b058-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="4b058-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="4b058-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="4b058-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="4b058-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="4b058-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="4b058-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="4b058-737">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="4b058-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="4b058-738">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="4b058-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="4b058-739">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="4b058-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="4b058-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="4b058-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="4b058-741">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="4b058-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="4b058-742">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="4b058-743">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="4b058-744">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="4b058-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="4b058-745">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="4b058-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="4b058-746">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="4b058-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="4b058-747">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="4b058-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="4b058-748">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="4b058-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="4b058-749">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="4b058-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="4b058-750">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="4b058-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="4b058-752">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="4b058-752">Configure Windows Server</span></span>

1. <span data-ttu-id="4b058-753">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="4b058-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="4b058-754">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-755">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="4b058-756">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="4b058-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="4b058-757">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="4b058-758">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="4b058-759">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="4b058-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="4b058-760">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="4b058-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="4b058-761">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="4b058-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="4b058-762">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-762">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="4b058-763">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="4b058-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="4b058-764">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="4b058-764">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="4b058-765">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="4b058-765">Install the required .NET Framework.</span></span> <span data-ttu-id="4b058-766">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="4b058-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="4b058-767">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="4b058-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="4b058-768">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="4b058-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="4b058-769">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="4b058-770">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="4b058-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="4b058-771">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="4b058-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="4b058-772">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="4b058-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="4b058-773">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="4b058-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="4b058-774">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="4b058-775">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="4b058-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="4b058-776">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="4b058-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="4b058-777">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="4b058-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="4b058-778">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="4b058-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="4b058-779">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="4b058-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="4b058-780">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="4b058-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="4b058-781">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="4b058-782">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="4b058-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="4b058-783">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="4b058-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4b058-784">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="4b058-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="4b058-785">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="4b058-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="4b058-786">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="4b058-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="4b058-787">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="4b058-788">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="4b058-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="4b058-789">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="4b058-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="4b058-790">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="4b058-790">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="4b058-791">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-792">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="4b058-793">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="4b058-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="4b058-794">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="4b058-794">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="4b058-795">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="4b058-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="4b058-796">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="4b058-797">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="4b058-798">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="4b058-799">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="4b058-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="4b058-800">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-800">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="4b058-801">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="4b058-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="4b058-802">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="4b058-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="4b058-803">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="4b058-803">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="4b058-804">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="4b058-804">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="4b058-805">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="4b058-805">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="4b058-806">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="4b058-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="4b058-807">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="4b058-807">In Visual Studio:</span></span>
     * <span data-ttu-id="4b058-808">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="4b058-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="4b058-809">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="4b058-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="4b058-810">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="4b058-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="4b058-811">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="4b058-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="4b058-812">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="4b058-812">Open the app's project file.</span></span>
     * <span data-ttu-id="4b058-813">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="4b058-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="4b058-814">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="4b058-814">In the following example:</span></span>

   * <span data-ttu-id="4b058-815">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="4b058-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="4b058-816">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="4b058-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="4b058-817">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="4b058-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="4b058-818">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="4b058-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="4b058-819">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="4b058-819">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="4b058-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="4b058-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="4b058-821">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="4b058-821">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="4b058-822">运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-822">Run the app.</span></span>

   <span data-ttu-id="4b058-823">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="4b058-824">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="4b058-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="4b058-825">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="4b058-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="4b058-826">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="4b058-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="4b058-827">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="4b058-827">A development certificate is used in this example.</span></span> <span data-ttu-id="4b058-828">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="4b058-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="4b058-830">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="4b058-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="4b058-831">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="4b058-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="4b058-832">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="4b058-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b058-833">其他资源</span><span class="sxs-lookup"><span data-stu-id="4b058-833">Additional resources</span></span>

* [<span data-ttu-id="4b058-834">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4b058-834">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="4b058-835">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="4b058-835">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="4b058-836">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="4b058-836">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="4b058-837">主机</span><span class="sxs-lookup"><span data-stu-id="4b058-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end