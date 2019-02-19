---
title: ASP.NET Core 中的 HTTP.sys Web 服务器实现
author: guardrex
description: 了解 Windows 上适用于 ASP.NET Core 的 Web 服务器 HTTP.sys。 HTTP.sys 构建于 HTTP.sys 内核模式驱动程序之上，是 Kestrel 的一种替代选择，可用来直接连接到 Internet，而无需使用 IIS。
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/13/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 859e3daeba125ab1a9392c1bdbf2733de2f79a34
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248337"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="f1f3a-104">ASP.NET Core 中的 HTTP.sys Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="f1f3a-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="f1f3a-105">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f1f3a-105">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f1f3a-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="f1f3a-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f1f3a-108">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="f1f3a-109">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="f1f3a-110">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="f1f3a-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="f1f3a-111">端口共享</span><span class="sxs-lookup"><span data-stu-id="f1f3a-111">Port sharing</span></span>
* <span data-ttu-id="f1f3a-112">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="f1f3a-112">HTTPS with SNI</span></span>
* <span data-ttu-id="f1f3a-113">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="f1f3a-114">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="f1f3a-114">Direct file transmission</span></span>
* <span data-ttu-id="f1f3a-115">响应缓存</span><span class="sxs-lookup"><span data-stu-id="f1f3a-115">Response caching</span></span>
* <span data-ttu-id="f1f3a-116">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="f1f3a-117">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-117">Supported Windows versions:</span></span>

* <span data-ttu-id="f1f3a-118">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f1f3a-118">Windows 7 or later</span></span>
* <span data-ttu-id="f1f3a-119">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f1f3a-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f1f3a-120">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-120">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="f1f3a-121">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="f1f3a-121">When to use HTTP.sys</span></span>

<span data-ttu-id="f1f3a-122">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="f1f3a-123">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="f1f3a-125">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="f1f3a-127">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="f1f3a-128">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="f1f3a-129">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="f1f3a-129">HTTP/2 support</span></span>

<span data-ttu-id="f1f3a-130">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f1f3a-131">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f1f3a-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="f1f3a-132">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="f1f3a-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f1f3a-133">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f1f3a-133">TLS 1.2 or later connection</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f1f3a-134">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f1f3a-135">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="f1f3a-136">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="f1f3a-137">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="f1f3a-138">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="f1f3a-139">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="f1f3a-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="f1f3a-140">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="f1f3a-141">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="f1f3a-142">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="f1f3a-143">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="f1f3a-144">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="f1f3a-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="f1f3a-145">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="f1f3a-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

1. <span data-ttu-id="f1f3a-146">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/))（ASP.NET Core 2.1 或更高版本）时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-146">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="f1f3a-147">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-147">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

2. <span data-ttu-id="f1f3a-148">生成 Web 主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，同时指定所需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the web host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>:</span></span>

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   <span data-ttu-id="f1f3a-149">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-149">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

   <span data-ttu-id="f1f3a-150">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="f1f3a-150">**HTTP.sys options**</span></span>

   | <span data-ttu-id="f1f3a-151">Property</span><span class="sxs-lookup"><span data-stu-id="f1f3a-151">Property</span></span> | <span data-ttu-id="f1f3a-152">说明​​</span><span class="sxs-lookup"><span data-stu-id="f1f3a-152">Description</span></span> | <span data-ttu-id="f1f3a-153">默认</span><span class="sxs-lookup"><span data-stu-id="f1f3a-153">Default</span></span> |
   | -------- | ----------- | :-----: |
   | [<span data-ttu-id="f1f3a-154">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="f1f3a-154">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="f1f3a-155">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-155">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
   | [<span data-ttu-id="f1f3a-156">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="f1f3a-156">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="f1f3a-157">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-157">Allow anonymous requests.</span></span> | `true` |
   | [<span data-ttu-id="f1f3a-158">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="f1f3a-158">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="f1f3a-159">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-159">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="f1f3a-160">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-160">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="f1f3a-161">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) `Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-161">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
   | [<span data-ttu-id="f1f3a-162">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="f1f3a-162">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="f1f3a-163">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-163">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="f1f3a-164">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-164">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="f1f3a-165">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-165">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="f1f3a-166">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-166">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="f1f3a-167">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="f1f3a-167">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="f1f3a-168">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-168">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="f1f3a-169">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-169">Use `-1` for infinite.</span></span> <span data-ttu-id="f1f3a-170">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-170">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="f1f3a-171">（无限制）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-171">(unlimited)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="f1f3a-172">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="f1f3a-173">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="f1f3a-173">30000000 bytes</span></span><br><span data-ttu-id="f1f3a-174">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="f1f3a-174">(~28.6 MB)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="f1f3a-175">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="f1f3a-176">1000</span><span class="sxs-lookup"><span data-stu-id="f1f3a-176">1000</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="f1f3a-177">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-177">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="f1f3a-178">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-178">(complete normally)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="f1f3a-179">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-179">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="f1f3a-180">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-180">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="f1f3a-181">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; HTTP 服务器 API 对保持的连接消耗实体正文的时间上限。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-181">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="f1f3a-182">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; 请求实体正文到达的时间上限。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-182">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="f1f3a-183">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; HTTP 服务器 API 分析请求头的时间上限。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-183">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="f1f3a-184">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; 空闲连接存在的时间上限。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-184">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="f1f3a-185">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; 响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-185">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="f1f3a-186">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; 请求在被应用选择前在请求队列中保留的时间上限。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-186">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="f1f3a-187">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-187">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="f1f3a-188">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-188">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="f1f3a-189">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-189">These may be modified at any time prior to disposing the listener.</span></span> |  |

   <a name="maxrequestbodysize"></a>

   <span data-ttu-id="f1f3a-190">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="f1f3a-190">**MaxRequestBodySize**</span></span>

   <span data-ttu-id="f1f3a-191">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-191">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="f1f3a-192">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-192">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="f1f3a-193">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-193">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

   <span data-ttu-id="f1f3a-194">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-194">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   <span data-ttu-id="f1f3a-195">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-195">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="f1f3a-196">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-196">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

   <span data-ttu-id="f1f3a-197">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-197">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. <span data-ttu-id="f1f3a-198">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-198">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

   <span data-ttu-id="f1f3a-199">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-199">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="f1f3a-200">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-200">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

   ![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="f1f3a-202">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="f1f3a-202">Configure Windows Server</span></span>

1. <span data-ttu-id="f1f3a-203">确定要为应用打开的端口，并使用 Windows 防火墙或 [PowerShell cmdlet](https://technet.microsoft.com/library/jj554906) 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-203">Determine the ports to open for the app and use Windows Firewall or [PowerShell cmdlets](https://technet.microsoft.com/library/jj554906) to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="f1f3a-204">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-network/security-overview)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-204">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-network/security-overview).</span></span> <span data-ttu-id="f1f3a-205">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-205">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="f1f3a-206">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-206">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="f1f3a-207">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-207">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="f1f3a-208">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-208">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="f1f3a-209">在服务器的“本地计算机” > “个人”存储中，安装自签名证书或 CA 签名证书。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-209">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="f1f3a-210">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-210">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="f1f3a-211">**.NET Core** &ndash; 如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-211">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="f1f3a-212">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-212">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="f1f3a-213">**.NET Framework** &ndash; 如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-213">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="f1f3a-214">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-214">Install the required .NET Framework.</span></span> <span data-ttu-id="f1f3a-215">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-215">The installer for the latest .NET Framework is available from from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="f1f3a-216">如果应用是[独立式部署](/dotnet/core/deploying/#framework-dependent-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-216">If the app is a [self-contained deployment](/dotnet/core/deploying/#framework-dependent-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="f1f3a-217">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-217">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="f1f3a-218">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-218">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="f1f3a-219">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-219">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="f1f3a-220">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-220">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="f1f3a-221">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="f1f3a-221">`urls` command-line argument</span></span>
   * <span data-ttu-id="f1f3a-222">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="f1f3a-222">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="f1f3a-223">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-223">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=11)]

   <span data-ttu-id="f1f3a-224">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-224">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="f1f3a-225">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-225">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="f1f3a-226">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-226">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span> <span data-ttu-id="f1f3a-227">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-227">For more information, see <xref:fundamentals/host/web-host>.</span></span>

   <span data-ttu-id="f1f3a-228">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-228">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="f1f3a-229">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-229">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="f1f3a-230">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-230">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="f1f3a-231">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-231">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="f1f3a-232">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-232">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="f1f3a-233">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-233">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="f1f3a-234">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-234">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="f1f3a-235">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-235">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="f1f3a-236">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-236">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="f1f3a-237">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-237">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="f1f3a-238">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-238">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="f1f3a-239">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-239">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="f1f3a-240">`<URL>` &ndash; 完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-240">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="f1f3a-241">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-241">Don't use a wildcard binding.</span></span> <span data-ttu-id="f1f3a-242">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-242">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="f1f3a-243">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-243">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="f1f3a-244">`<USER>` &ndash; 指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-244">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="f1f3a-245">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-245">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="f1f3a-246">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-246">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="f1f3a-247">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-247">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="f1f3a-248">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-248">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="f1f3a-249">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-249">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="f1f3a-250">`<IP>` &ndash; 指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-250">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="f1f3a-251">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-251">Don't use a wildcard binding.</span></span> <span data-ttu-id="f1f3a-252">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-252">Use a valid IP address.</span></span>
   * <span data-ttu-id="f1f3a-253">`<PORT>` &ndash; 指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-253">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="f1f3a-254">`<THUMBPRINT>` &ndash; X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-254">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="f1f3a-255">`<GUID>` &ndash; 开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-255">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="f1f3a-256">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-256">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="f1f3a-257">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-257">In Visual Studio:</span></span>
     * <span data-ttu-id="f1f3a-258">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-258">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="f1f3a-259">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-259">Select the **Package** tab.</span></span>
     * <span data-ttu-id="f1f3a-260">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-260">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="f1f3a-261">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-261">When not using Visual Studio:</span></span>
     * <span data-ttu-id="f1f3a-262">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-262">Open the app's project file.</span></span>
     * <span data-ttu-id="f1f3a-263">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-263">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="f1f3a-264">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-264">In the following example:</span></span>

   * <span data-ttu-id="f1f3a-265">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-265">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="f1f3a-266">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-266">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="f1f3a-267">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-267">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="f1f3a-268">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-268">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="f1f3a-269">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="f1f3a-269">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="f1f3a-270">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-270">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="f1f3a-271">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-271">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="f1f3a-272">运行应用。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-272">Run the app.</span></span>

   <span data-ttu-id="f1f3a-273">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-273">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="f1f3a-274">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-274">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="f1f3a-275">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-275">The app responds at the server's public IP address.</span></span> <span data-ttu-id="f1f3a-276">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-276">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="f1f3a-277">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-277">A development certificate is used in this example.</span></span> <span data-ttu-id="f1f3a-278">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-278">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f1f3a-280">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f1f3a-280">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f1f3a-281">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-281">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="f1f3a-282">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="f1f3a-282">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1f3a-283">其他资源</span><span class="sxs-lookup"><span data-stu-id="f1f3a-283">Additional resources</span></span>

* [<span data-ttu-id="f1f3a-284">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="f1f3a-284">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#enable-windows-authentication-with-httpsys)
* [<span data-ttu-id="f1f3a-285">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="f1f3a-285">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="f1f3a-286">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="f1f3a-286">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* <xref:fundamentals/host/index>
* <xref:test/troubleshoot>
