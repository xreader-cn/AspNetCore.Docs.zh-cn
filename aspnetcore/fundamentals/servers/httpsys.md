---
<span data-ttu-id="82d9a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-102">'Blazor'</span></span>
- <span data-ttu-id="82d9a-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-103">'Identity'</span></span>
- <span data-ttu-id="82d9a-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-105">'Razor'</span></span>
- <span data-ttu-id="82d9a-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-106">'SignalR' uid:</span></span> 

---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="82d9a-107">ASP.NET Core 中的 HTTP.sys Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="82d9a-107">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="82d9a-108">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="82d9a-108">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="82d9a-109">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-109">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="82d9a-110">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-110">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="82d9a-111">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-111">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-112">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="82d9a-112">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="82d9a-113">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-113">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="82d9a-114">端口共享</span><span class="sxs-lookup"><span data-stu-id="82d9a-114">Port sharing</span></span>
* <span data-ttu-id="82d9a-115">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="82d9a-115">HTTPS with SNI</span></span>
* <span data-ttu-id="82d9a-116">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-116">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="82d9a-117">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="82d9a-117">Direct file transmission</span></span>
* <span data-ttu-id="82d9a-118">响应缓存</span><span class="sxs-lookup"><span data-stu-id="82d9a-118">Response caching</span></span>
* <span data-ttu-id="82d9a-119">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-119">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="82d9a-120">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="82d9a-120">Supported Windows versions:</span></span>

* <span data-ttu-id="82d9a-121">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-121">Windows 7 or later</span></span>
* <span data-ttu-id="82d9a-122">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-122">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="82d9a-123">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="82d9a-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="82d9a-124">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-124">When to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-125">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="82d9a-125">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="82d9a-126">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="82d9a-126">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="82d9a-128">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-128">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="82d9a-130">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-130">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="82d9a-131">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="82d9a-131">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="82d9a-132">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="82d9a-132">HTTP/2 support</span></span>

<span data-ttu-id="82d9a-133">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="82d9a-133">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="82d9a-134">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-134">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="82d9a-135">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-135">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="82d9a-136">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-136">TLS 1.2 or later connection</span></span>

<span data-ttu-id="82d9a-137">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-137">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="82d9a-138">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="82d9a-138">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="82d9a-139">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="82d9a-139">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="82d9a-140">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-140">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="82d9a-141">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-141">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="82d9a-142">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-142">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="82d9a-143">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-143">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="82d9a-144">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-144">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="82d9a-145">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="82d9a-145">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="82d9a-146">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-146">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="82d9a-147">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-147">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-148">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="82d9a-149">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-149">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="82d9a-150">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-150">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="82d9a-151">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="82d9a-151">**HTTP.sys options**</span></span>

| <span data-ttu-id="82d9a-152">Property</span><span class="sxs-lookup"><span data-stu-id="82d9a-152">Property</span></span> | <span data-ttu-id="82d9a-153">描述</span><span class="sxs-lookup"><span data-stu-id="82d9a-153">Description</span></span> | <span data-ttu-id="82d9a-154">默认</span><span class="sxs-lookup"><span data-stu-id="82d9a-154">Default</span></span> |
| ---
<span data-ttu-id="82d9a-155">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-155">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-156">'Blazor'</span></span>
- <span data-ttu-id="82d9a-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-157">'Identity'</span></span>
- <span data-ttu-id="82d9a-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-159">'Razor'</span></span>
- <span data-ttu-id="82d9a-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-161">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-161">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-162">'Blazor'</span></span>
- <span data-ttu-id="82d9a-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-163">'Identity'</span></span>
- <span data-ttu-id="82d9a-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-165">'Razor'</span></span>
- <span data-ttu-id="82d9a-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-166">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-167">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-167">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-168">'Blazor'</span></span>
- <span data-ttu-id="82d9a-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-169">'Identity'</span></span>
- <span data-ttu-id="82d9a-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-171">'Razor'</span></span>
- <span data-ttu-id="82d9a-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-173">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-173">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-174">'Blazor'</span></span>
- <span data-ttu-id="82d9a-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-175">'Identity'</span></span>
- <span data-ttu-id="82d9a-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-177">'Razor'</span></span>
- <span data-ttu-id="82d9a-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-179">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-179">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-180">'Blazor'</span></span>
- <span data-ttu-id="82d9a-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-181">'Identity'</span></span>
- <span data-ttu-id="82d9a-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-183">'Razor'</span></span>
- <span data-ttu-id="82d9a-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-184">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-185">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | 控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="82d9a-185">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="82d9a-186"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="82d9a-186"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="82d9a-187"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="82d9a-187"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="82d9a-188">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-188">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-189">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-189">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="82d9a-190"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-190"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="82d9a-191">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-191">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="82d9a-192">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-192">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="82d9a-193"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="82d9a-193"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="82d9a-194">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-194">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="82d9a-195">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="82d9a-195">Use `-1` for infinite.</span></span> <span data-ttu-id="82d9a-196">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-196">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="82d9a-197">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="82d9a-197">(machine-wide</span></span><br><span data-ttu-id="82d9a-198">设置）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="82d9a-198">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="82d9a-199">| 30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="82d9a-199">| 30000000 bytes</span></span><br><span data-ttu-id="82d9a-200">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-200">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="82d9a-201">| 1000 | | `RequestQueueMode` | 这指示服务器是否负责创建和配置请求队列，或是否应附加到现有队列。</span><span class="sxs-lookup"><span data-stu-id="82d9a-201">| 1000 | | `RequestQueueMode` | This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="82d9a-202">附加到现有队列时，大多数现有配置选项不适用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-202">Most existing configuration options do not apply when attaching to an existing queue.</span></span><span data-ttu-id="82d9a-203"> | `RequestQueueMode.Create` | | `RequestQueueName` | HTTP.sys 请求队列的名称。</span><span class="sxs-lookup"><span data-stu-id="82d9a-203"> | `RequestQueueMode.Create` | | `RequestQueueName` | The name of the HTTP.sys request queue.</span></span><span data-ttu-id="82d9a-204"> | `null` (Anonymous queue) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="82d9a-204"> | `null` (Anonymous queue) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="82d9a-205">（正常完成）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-205">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="82d9a-206">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-206">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="82d9a-207">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-207">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="82d9a-208">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-208">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="82d9a-209">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-209">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="82d9a-210">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-210">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="82d9a-211">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="82d9a-211">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="82d9a-212">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-212">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="82d9a-213">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | 指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-213">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="82d9a-214">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="82d9a-214">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="82d9a-215">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-215">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-216">|  |</span><span class="sxs-lookup"><span data-stu-id="82d9a-216">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="82d9a-217">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="82d9a-217">**MaxRequestBodySize**</span></span>

<span data-ttu-id="82d9a-218">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-218">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="82d9a-219">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-219">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="82d9a-220">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-220">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="82d9a-221">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="82d9a-221">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="82d9a-222">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="82d9a-222">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="82d9a-223">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-223">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="82d9a-224">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="82d9a-224">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="82d9a-225">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="82d9a-225">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-226">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="82d9a-226">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="82d9a-227">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="82d9a-227">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="82d9a-229">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="82d9a-229">Configure Windows Server</span></span>

1. <span data-ttu-id="82d9a-230">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="82d9a-230">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="82d9a-231">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-231">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-232">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-232">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="82d9a-233">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-233">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-234">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-234">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="82d9a-235">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-235">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="82d9a-236">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-236">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="82d9a-237">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="82d9a-237">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="82d9a-238">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-238">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="82d9a-239">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-239">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="82d9a-240">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="82d9a-240">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="82d9a-241">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-241">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="82d9a-242">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="82d9a-242">Install the required .NET Framework.</span></span> <span data-ttu-id="82d9a-243">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-243">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="82d9a-244">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="82d9a-244">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="82d9a-245">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="82d9a-245">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="82d9a-246">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-246">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="82d9a-247">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-247">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="82d9a-248">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="82d9a-248">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="82d9a-249">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="82d9a-249">`urls` command-line argument</span></span>
   * <span data-ttu-id="82d9a-250">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="82d9a-250">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="82d9a-251">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-251">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="82d9a-252">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="82d9a-252">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="82d9a-253">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-253">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="82d9a-254">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="82d9a-254">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="82d9a-255">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-255">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="82d9a-256">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-256">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="82d9a-257">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="82d9a-257">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="82d9a-258">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-258">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="82d9a-259">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-259">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="82d9a-260">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="82d9a-260">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="82d9a-261">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-261">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="82d9a-262">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="82d9a-262">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="82d9a-263">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="82d9a-263">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="82d9a-264">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-264">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="82d9a-265">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="82d9a-265">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="82d9a-266">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="82d9a-266">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="82d9a-267">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-267">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="82d9a-268">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-268">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-269">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-269">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="82d9a-270">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="82d9a-270">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="82d9a-271">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="82d9a-271">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="82d9a-272">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-272">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="82d9a-273">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-273">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="82d9a-274">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-274">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="82d9a-275">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-275">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="82d9a-276">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="82d9a-276">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="82d9a-277">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-277">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="82d9a-278">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-278">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-279">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-279">Use a valid IP address.</span></span>
   * <span data-ttu-id="82d9a-280">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-280">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="82d9a-281">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="82d9a-281">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="82d9a-282">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="82d9a-282">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="82d9a-283">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-283">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="82d9a-284">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-284">In Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-285">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="82d9a-285">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="82d9a-286">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="82d9a-286">Select the **Package** tab.</span></span>
     * <span data-ttu-id="82d9a-287">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="82d9a-287">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="82d9a-288">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="82d9a-288">When not using Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-289">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="82d9a-289">Open the app's project file.</span></span>
     * <span data-ttu-id="82d9a-290">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-290">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="82d9a-291">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-291">In the following example:</span></span>

   * <span data-ttu-id="82d9a-292">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-292">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="82d9a-293">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-293">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="82d9a-294">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-294">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="82d9a-295">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-295">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="82d9a-296">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="82d9a-296">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="82d9a-297">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="82d9a-297">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="82d9a-298">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="82d9a-298">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="82d9a-299">运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-299">Run the app.</span></span>

   <span data-ttu-id="82d9a-300">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-300">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="82d9a-301">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-301">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="82d9a-302">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="82d9a-302">The app responds at the server's public IP address.</span></span> <span data-ttu-id="82d9a-303">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-303">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="82d9a-304">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-304">A development certificate is used in this example.</span></span> <span data-ttu-id="82d9a-305">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="82d9a-305">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="82d9a-307">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="82d9a-307">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="82d9a-308">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-308">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="82d9a-309">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-309">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82d9a-310">其他资源</span><span class="sxs-lookup"><span data-stu-id="82d9a-310">Additional resources</span></span>

* [<span data-ttu-id="82d9a-311">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-311">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="82d9a-312">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="82d9a-312">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="82d9a-313">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="82d9a-313">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="82d9a-314">主机</span><span class="sxs-lookup"><span data-stu-id="82d9a-314">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="82d9a-315">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-315">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="82d9a-316">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-316">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="82d9a-317">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-317">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-318">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="82d9a-318">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="82d9a-319">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-319">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="82d9a-320">端口共享</span><span class="sxs-lookup"><span data-stu-id="82d9a-320">Port sharing</span></span>
* <span data-ttu-id="82d9a-321">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="82d9a-321">HTTPS with SNI</span></span>
* <span data-ttu-id="82d9a-322">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-322">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="82d9a-323">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="82d9a-323">Direct file transmission</span></span>
* <span data-ttu-id="82d9a-324">响应缓存</span><span class="sxs-lookup"><span data-stu-id="82d9a-324">Response caching</span></span>
* <span data-ttu-id="82d9a-325">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-325">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="82d9a-326">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="82d9a-326">Supported Windows versions:</span></span>

* <span data-ttu-id="82d9a-327">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-327">Windows 7 or later</span></span>
* <span data-ttu-id="82d9a-328">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-328">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="82d9a-329">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="82d9a-329">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="82d9a-330">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-330">When to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-331">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="82d9a-331">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="82d9a-332">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="82d9a-332">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="82d9a-334">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-334">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="82d9a-336">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-336">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="82d9a-337">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="82d9a-337">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="82d9a-338">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="82d9a-338">HTTP/2 support</span></span>

<span data-ttu-id="82d9a-339">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="82d9a-339">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="82d9a-340">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-340">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="82d9a-341">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-341">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="82d9a-342">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-342">TLS 1.2 or later connection</span></span>

<span data-ttu-id="82d9a-343">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-343">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="82d9a-344">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="82d9a-344">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="82d9a-345">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="82d9a-345">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="82d9a-346">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-346">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="82d9a-347">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-347">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="82d9a-348">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-348">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="82d9a-349">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-349">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="82d9a-350">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-350">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="82d9a-351">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="82d9a-351">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="82d9a-352">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-352">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="82d9a-353">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-353">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-354">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-354">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="82d9a-355">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-355">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="82d9a-356">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-356">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="82d9a-357">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="82d9a-357">**HTTP.sys options**</span></span>

| <span data-ttu-id="82d9a-358">Property</span><span class="sxs-lookup"><span data-stu-id="82d9a-358">Property</span></span> | <span data-ttu-id="82d9a-359">描述</span><span class="sxs-lookup"><span data-stu-id="82d9a-359">Description</span></span> | <span data-ttu-id="82d9a-360">默认</span><span class="sxs-lookup"><span data-stu-id="82d9a-360">Default</span></span> |
| ---
<span data-ttu-id="82d9a-361">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-361">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-362">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-362">'Blazor'</span></span>
- <span data-ttu-id="82d9a-363">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-363">'Identity'</span></span>
- <span data-ttu-id="82d9a-364">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-364">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-365">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-365">'Razor'</span></span>
- <span data-ttu-id="82d9a-366">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-366">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-367">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-367">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-368">'Blazor'</span></span>
- <span data-ttu-id="82d9a-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-369">'Identity'</span></span>
- <span data-ttu-id="82d9a-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-371">'Razor'</span></span>
- <span data-ttu-id="82d9a-372">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-372">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-373">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-373">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-374">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-374">'Blazor'</span></span>
- <span data-ttu-id="82d9a-375">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-375">'Identity'</span></span>
- <span data-ttu-id="82d9a-376">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-376">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-377">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-377">'Razor'</span></span>
- <span data-ttu-id="82d9a-378">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-378">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-379">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-379">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-380">'Blazor'</span></span>
- <span data-ttu-id="82d9a-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-381">'Identity'</span></span>
- <span data-ttu-id="82d9a-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-383">'Razor'</span></span>
- <span data-ttu-id="82d9a-384">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-385">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-385">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-386">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-386">'Blazor'</span></span>
- <span data-ttu-id="82d9a-387">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-387">'Identity'</span></span>
- <span data-ttu-id="82d9a-388">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-388">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-389">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-389">'Razor'</span></span>
- <span data-ttu-id="82d9a-390">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-390">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-391">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | 控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="82d9a-391">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="82d9a-392"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="82d9a-392"> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="82d9a-393"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="82d9a-393"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="82d9a-394">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-394">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-395">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-395">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="82d9a-396"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-396"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="82d9a-397">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-397">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="82d9a-398">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-398">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="82d9a-399"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="82d9a-399"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="82d9a-400">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-400">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="82d9a-401">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="82d9a-401">Use `-1` for infinite.</span></span> <span data-ttu-id="82d9a-402">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-402">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="82d9a-403">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="82d9a-403">(machine-wide</span></span><br><span data-ttu-id="82d9a-404">设置）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="82d9a-404">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="82d9a-405">| 30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="82d9a-405">| 30000000 bytes</span></span><br><span data-ttu-id="82d9a-406">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-406">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="82d9a-407">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="82d9a-407">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="82d9a-408">（正常完成）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-408">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="82d9a-409">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-409">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="82d9a-410">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-410">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="82d9a-411">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-411">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="82d9a-412">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-412">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="82d9a-413">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-413">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="82d9a-414">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="82d9a-414">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="82d9a-415">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-415">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="82d9a-416">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | 指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-416">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="82d9a-417">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="82d9a-417">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="82d9a-418">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-418">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-419">|  |</span><span class="sxs-lookup"><span data-stu-id="82d9a-419">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="82d9a-420">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="82d9a-420">**MaxRequestBodySize**</span></span>

<span data-ttu-id="82d9a-421">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-421">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="82d9a-422">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-422">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="82d9a-423">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-423">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="82d9a-424">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="82d9a-424">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="82d9a-425">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="82d9a-425">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="82d9a-426">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-426">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="82d9a-427">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="82d9a-427">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="82d9a-428">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="82d9a-428">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-429">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="82d9a-429">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="82d9a-430">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="82d9a-430">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="82d9a-432">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="82d9a-432">Configure Windows Server</span></span>

1. <span data-ttu-id="82d9a-433">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="82d9a-433">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="82d9a-434">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-434">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-435">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-435">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="82d9a-436">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-436">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-437">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-437">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="82d9a-438">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-438">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="82d9a-439">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-439">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="82d9a-440">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="82d9a-440">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="82d9a-441">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-441">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="82d9a-442">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-442">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="82d9a-443">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="82d9a-443">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="82d9a-444">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-444">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="82d9a-445">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="82d9a-445">Install the required .NET Framework.</span></span> <span data-ttu-id="82d9a-446">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-446">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="82d9a-447">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="82d9a-447">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="82d9a-448">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="82d9a-448">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="82d9a-449">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-449">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="82d9a-450">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-450">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="82d9a-451">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="82d9a-451">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="82d9a-452">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="82d9a-452">`urls` command-line argument</span></span>
   * <span data-ttu-id="82d9a-453">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="82d9a-453">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="82d9a-454">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-454">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="82d9a-455">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="82d9a-455">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="82d9a-456">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-456">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="82d9a-457">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="82d9a-457">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="82d9a-458">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-458">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="82d9a-459">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-459">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="82d9a-460">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="82d9a-460">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="82d9a-461">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-461">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="82d9a-462">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-462">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="82d9a-463">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="82d9a-463">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="82d9a-464">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-464">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="82d9a-465">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="82d9a-465">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="82d9a-466">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="82d9a-466">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="82d9a-467">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-467">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="82d9a-468">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="82d9a-468">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="82d9a-469">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="82d9a-469">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="82d9a-470">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-470">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="82d9a-471">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-471">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-472">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-472">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="82d9a-473">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="82d9a-473">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="82d9a-474">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="82d9a-474">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="82d9a-475">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-475">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="82d9a-476">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-476">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="82d9a-477">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-477">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="82d9a-478">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-478">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="82d9a-479">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="82d9a-479">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="82d9a-480">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-480">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="82d9a-481">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-481">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-482">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-482">Use a valid IP address.</span></span>
   * <span data-ttu-id="82d9a-483">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-483">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="82d9a-484">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="82d9a-484">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="82d9a-485">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="82d9a-485">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="82d9a-486">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-486">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="82d9a-487">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-487">In Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-488">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="82d9a-488">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="82d9a-489">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="82d9a-489">Select the **Package** tab.</span></span>
     * <span data-ttu-id="82d9a-490">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="82d9a-490">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="82d9a-491">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="82d9a-491">When not using Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-492">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="82d9a-492">Open the app's project file.</span></span>
     * <span data-ttu-id="82d9a-493">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-493">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="82d9a-494">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-494">In the following example:</span></span>

   * <span data-ttu-id="82d9a-495">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-495">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="82d9a-496">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-496">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="82d9a-497">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-497">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="82d9a-498">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-498">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="82d9a-499">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="82d9a-499">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="82d9a-500">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="82d9a-500">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="82d9a-501">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="82d9a-501">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="82d9a-502">运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-502">Run the app.</span></span>

   <span data-ttu-id="82d9a-503">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-503">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="82d9a-504">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-504">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="82d9a-505">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="82d9a-505">The app responds at the server's public IP address.</span></span> <span data-ttu-id="82d9a-506">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-506">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="82d9a-507">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-507">A development certificate is used in this example.</span></span> <span data-ttu-id="82d9a-508">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="82d9a-508">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="82d9a-510">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="82d9a-510">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="82d9a-511">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-511">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="82d9a-512">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-512">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82d9a-513">其他资源</span><span class="sxs-lookup"><span data-stu-id="82d9a-513">Additional resources</span></span>

* [<span data-ttu-id="82d9a-514">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-514">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="82d9a-515">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="82d9a-515">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="82d9a-516">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="82d9a-516">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="82d9a-517">主机</span><span class="sxs-lookup"><span data-stu-id="82d9a-517">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="82d9a-518">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-518">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="82d9a-519">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-519">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="82d9a-520">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-520">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-521">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="82d9a-521">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="82d9a-522">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-522">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="82d9a-523">端口共享</span><span class="sxs-lookup"><span data-stu-id="82d9a-523">Port sharing</span></span>
* <span data-ttu-id="82d9a-524">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="82d9a-524">HTTPS with SNI</span></span>
* <span data-ttu-id="82d9a-525">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-525">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="82d9a-526">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="82d9a-526">Direct file transmission</span></span>
* <span data-ttu-id="82d9a-527">响应缓存</span><span class="sxs-lookup"><span data-stu-id="82d9a-527">Response caching</span></span>
* <span data-ttu-id="82d9a-528">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-528">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="82d9a-529">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="82d9a-529">Supported Windows versions:</span></span>

* <span data-ttu-id="82d9a-530">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-530">Windows 7 or later</span></span>
* <span data-ttu-id="82d9a-531">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-531">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="82d9a-532">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="82d9a-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="82d9a-533">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-533">When to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-534">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="82d9a-534">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="82d9a-535">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="82d9a-535">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="82d9a-537">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-537">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="82d9a-539">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-539">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="82d9a-540">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="82d9a-540">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="82d9a-541">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="82d9a-541">HTTP/2 support</span></span>

<span data-ttu-id="82d9a-542">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="82d9a-542">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="82d9a-543">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-543">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="82d9a-544">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-544">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="82d9a-545">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-545">TLS 1.2 or later connection</span></span>

<span data-ttu-id="82d9a-546">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-546">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="82d9a-547">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="82d9a-547">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="82d9a-548">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="82d9a-548">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="82d9a-549">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-549">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="82d9a-550">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-550">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="82d9a-551">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-551">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="82d9a-552">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-552">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="82d9a-553">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-553">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="82d9a-554">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="82d9a-554">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="82d9a-555">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-555">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="82d9a-556">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-556">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-557">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-557">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="82d9a-558">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-558">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="82d9a-559">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-559">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="82d9a-560">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-560">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="82d9a-561">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-561">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="82d9a-562">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="82d9a-562">**HTTP.sys options**</span></span>

| <span data-ttu-id="82d9a-563">Property</span><span class="sxs-lookup"><span data-stu-id="82d9a-563">Property</span></span> | <span data-ttu-id="82d9a-564">描述</span><span class="sxs-lookup"><span data-stu-id="82d9a-564">Description</span></span> | <span data-ttu-id="82d9a-565">默认</span><span class="sxs-lookup"><span data-stu-id="82d9a-565">Default</span></span> |
| ---
<span data-ttu-id="82d9a-566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-567">'Blazor'</span></span>
- <span data-ttu-id="82d9a-568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-568">'Identity'</span></span>
- <span data-ttu-id="82d9a-569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-569">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-570">'Razor'</span></span>
- <span data-ttu-id="82d9a-571">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-573">'Blazor'</span></span>
- <span data-ttu-id="82d9a-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-574">'Identity'</span></span>
- <span data-ttu-id="82d9a-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-576">'Razor'</span></span>
- <span data-ttu-id="82d9a-577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-577">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-578">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-578">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-579">'Blazor'</span></span>
- <span data-ttu-id="82d9a-580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-580">'Identity'</span></span>
- <span data-ttu-id="82d9a-581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-581">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-582">'Razor'</span></span>
- <span data-ttu-id="82d9a-583">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-585">'Blazor'</span></span>
- <span data-ttu-id="82d9a-586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-586">'Identity'</span></span>
- <span data-ttu-id="82d9a-587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-587">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-588">'Razor'</span></span>
- <span data-ttu-id="82d9a-589">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-591">'Blazor'</span></span>
- <span data-ttu-id="82d9a-592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-592">'Identity'</span></span>
- <span data-ttu-id="82d9a-593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-593">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-594">'Razor'</span></span>
- <span data-ttu-id="82d9a-595">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-595">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-596">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | 控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="82d9a-596">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="82d9a-597"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="82d9a-597"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="82d9a-598"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="82d9a-598"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="82d9a-599">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-599">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-600">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-600">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="82d9a-601"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-601"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="82d9a-602">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-602">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="82d9a-603">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-603">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="82d9a-604"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="82d9a-604"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="82d9a-605">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-605">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="82d9a-606">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="82d9a-606">Use `-1` for infinite.</span></span> <span data-ttu-id="82d9a-607">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-607">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="82d9a-608">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="82d9a-608">(machine-wide</span></span><br><span data-ttu-id="82d9a-609">设置）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="82d9a-609">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="82d9a-610">| 30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="82d9a-610">| 30000000 bytes</span></span><br><span data-ttu-id="82d9a-611">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-611">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="82d9a-612">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="82d9a-612">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="82d9a-613">（正常完成）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-613">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="82d9a-614">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-614">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="82d9a-615">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-615">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="82d9a-616">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-616">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="82d9a-617">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-617">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="82d9a-618">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-618">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="82d9a-619">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="82d9a-619">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="82d9a-620">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-620">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="82d9a-621">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | 指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-621">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="82d9a-622">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="82d9a-622">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="82d9a-623">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-623">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-624">|  |</span><span class="sxs-lookup"><span data-stu-id="82d9a-624">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="82d9a-625">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="82d9a-625">**MaxRequestBodySize**</span></span>

<span data-ttu-id="82d9a-626">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-626">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="82d9a-627">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-627">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="82d9a-628">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-628">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="82d9a-629">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="82d9a-629">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="82d9a-630">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="82d9a-630">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="82d9a-631">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-631">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="82d9a-632">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="82d9a-632">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="82d9a-633">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="82d9a-633">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-634">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="82d9a-634">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="82d9a-635">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="82d9a-635">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="82d9a-637">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="82d9a-637">Configure Windows Server</span></span>

1. <span data-ttu-id="82d9a-638">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="82d9a-638">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="82d9a-639">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-639">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-640">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-640">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="82d9a-641">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-641">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-642">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-642">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="82d9a-643">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-643">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="82d9a-644">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-644">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="82d9a-645">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="82d9a-645">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="82d9a-646">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-646">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="82d9a-647">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-647">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="82d9a-648">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="82d9a-648">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="82d9a-649">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-649">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="82d9a-650">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="82d9a-650">Install the required .NET Framework.</span></span> <span data-ttu-id="82d9a-651">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-651">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="82d9a-652">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="82d9a-652">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="82d9a-653">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="82d9a-653">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="82d9a-654">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-654">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="82d9a-655">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-655">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="82d9a-656">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="82d9a-656">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="82d9a-657">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="82d9a-657">`urls` command-line argument</span></span>
   * <span data-ttu-id="82d9a-658">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="82d9a-658">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="82d9a-659">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-659">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="82d9a-660">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="82d9a-660">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="82d9a-661">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-661">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="82d9a-662">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="82d9a-662">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="82d9a-663">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-663">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="82d9a-664">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-664">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="82d9a-665">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="82d9a-665">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="82d9a-666">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-666">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="82d9a-667">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-667">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="82d9a-668">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="82d9a-668">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="82d9a-669">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-669">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="82d9a-670">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="82d9a-670">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="82d9a-671">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="82d9a-671">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="82d9a-672">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-672">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="82d9a-673">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="82d9a-673">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="82d9a-674">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="82d9a-674">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="82d9a-675">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-675">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="82d9a-676">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-676">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-677">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-677">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="82d9a-678">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="82d9a-678">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="82d9a-679">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="82d9a-679">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="82d9a-680">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-680">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="82d9a-681">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-681">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="82d9a-682">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-682">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="82d9a-683">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-683">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="82d9a-684">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="82d9a-684">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="82d9a-685">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-685">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="82d9a-686">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-686">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-687">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-687">Use a valid IP address.</span></span>
   * <span data-ttu-id="82d9a-688">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-688">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="82d9a-689">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="82d9a-689">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="82d9a-690">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="82d9a-690">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="82d9a-691">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-691">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="82d9a-692">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-692">In Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-693">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="82d9a-693">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="82d9a-694">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="82d9a-694">Select the **Package** tab.</span></span>
     * <span data-ttu-id="82d9a-695">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="82d9a-695">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="82d9a-696">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="82d9a-696">When not using Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-697">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="82d9a-697">Open the app's project file.</span></span>
     * <span data-ttu-id="82d9a-698">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-698">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="82d9a-699">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-699">In the following example:</span></span>

   * <span data-ttu-id="82d9a-700">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-700">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="82d9a-701">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-701">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="82d9a-702">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-702">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="82d9a-703">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-703">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="82d9a-704">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="82d9a-704">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="82d9a-705">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="82d9a-705">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="82d9a-706">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="82d9a-706">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="82d9a-707">运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-707">Run the app.</span></span>

   <span data-ttu-id="82d9a-708">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-708">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="82d9a-709">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-709">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="82d9a-710">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="82d9a-710">The app responds at the server's public IP address.</span></span> <span data-ttu-id="82d9a-711">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-711">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="82d9a-712">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-712">A development certificate is used in this example.</span></span> <span data-ttu-id="82d9a-713">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="82d9a-713">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="82d9a-715">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="82d9a-715">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="82d9a-716">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-716">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="82d9a-717">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-717">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82d9a-718">其他资源</span><span class="sxs-lookup"><span data-stu-id="82d9a-718">Additional resources</span></span>

* [<span data-ttu-id="82d9a-719">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-719">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="82d9a-720">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="82d9a-720">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="82d9a-721">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="82d9a-721">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="82d9a-722">主机</span><span class="sxs-lookup"><span data-stu-id="82d9a-722">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="82d9a-723">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-723">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="82d9a-724">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-724">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="82d9a-725">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-725">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-726">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="82d9a-726">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="82d9a-727">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-727">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="82d9a-728">端口共享</span><span class="sxs-lookup"><span data-stu-id="82d9a-728">Port sharing</span></span>
* <span data-ttu-id="82d9a-729">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="82d9a-729">HTTPS with SNI</span></span>
* <span data-ttu-id="82d9a-730">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-730">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="82d9a-731">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="82d9a-731">Direct file transmission</span></span>
* <span data-ttu-id="82d9a-732">响应缓存</span><span class="sxs-lookup"><span data-stu-id="82d9a-732">Response caching</span></span>
* <span data-ttu-id="82d9a-733">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="82d9a-733">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="82d9a-734">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="82d9a-734">Supported Windows versions:</span></span>

* <span data-ttu-id="82d9a-735">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-735">Windows 7 or later</span></span>
* <span data-ttu-id="82d9a-736">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-736">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="82d9a-737">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="82d9a-737">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="82d9a-738">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-738">When to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-739">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="82d9a-739">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="82d9a-740">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="82d9a-740">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="82d9a-742">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-742">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="82d9a-744">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-744">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="82d9a-745">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="82d9a-745">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="82d9a-746">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="82d9a-746">HTTP/2 support</span></span>

<span data-ttu-id="82d9a-747">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="82d9a-747">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="82d9a-748">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="82d9a-748">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="82d9a-749">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-749">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="82d9a-750">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="82d9a-750">TLS 1.2 or later connection</span></span>

<span data-ttu-id="82d9a-751">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-751">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="82d9a-752">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="82d9a-752">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="82d9a-753">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="82d9a-753">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="82d9a-754">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="82d9a-754">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="82d9a-755">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-755">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="82d9a-756">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-756">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="82d9a-757">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-757">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="82d9a-758">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="82d9a-758">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="82d9a-759">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="82d9a-759">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="82d9a-760">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-760">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="82d9a-761">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="82d9a-761">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="82d9a-762">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-762">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="82d9a-763">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-763">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="82d9a-764">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-764">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="82d9a-765">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-765">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="82d9a-766">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-766">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="82d9a-767">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="82d9a-767">**HTTP.sys options**</span></span>

| <span data-ttu-id="82d9a-768">Property</span><span class="sxs-lookup"><span data-stu-id="82d9a-768">Property</span></span> | <span data-ttu-id="82d9a-769">描述</span><span class="sxs-lookup"><span data-stu-id="82d9a-769">Description</span></span> | <span data-ttu-id="82d9a-770">默认</span><span class="sxs-lookup"><span data-stu-id="82d9a-770">Default</span></span> |
| ---
<span data-ttu-id="82d9a-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-772">'Blazor'</span></span>
- <span data-ttu-id="82d9a-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-773">'Identity'</span></span>
- <span data-ttu-id="82d9a-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-775">'Razor'</span></span>
- <span data-ttu-id="82d9a-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-778">'Blazor'</span></span>
- <span data-ttu-id="82d9a-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-779">'Identity'</span></span>
- <span data-ttu-id="82d9a-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-781">'Razor'</span></span>
- <span data-ttu-id="82d9a-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-782">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-783">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-783">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-784">'Blazor'</span></span>
- <span data-ttu-id="82d9a-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-785">'Identity'</span></span>
- <span data-ttu-id="82d9a-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-787">'Razor'</span></span>
- <span data-ttu-id="82d9a-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-790">'Blazor'</span></span>
- <span data-ttu-id="82d9a-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-791">'Identity'</span></span>
- <span data-ttu-id="82d9a-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-793">'Razor'</span></span>
- <span data-ttu-id="82d9a-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="82d9a-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="82d9a-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="82d9a-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-796">'Blazor'</span></span>
- <span data-ttu-id="82d9a-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="82d9a-797">'Identity'</span></span>
- <span data-ttu-id="82d9a-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="82d9a-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="82d9a-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="82d9a-799">'Razor'</span></span>
- <span data-ttu-id="82d9a-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="82d9a-800">'SignalR' uid:</span></span> 

<span data-ttu-id="82d9a-801">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | 控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="82d9a-801">------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span><span data-ttu-id="82d9a-802"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | 允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="82d9a-802"> | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Allow anonymous requests.</span></span><span data-ttu-id="82d9a-803"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | 指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="82d9a-803"> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Specify the allowed authentication schemes.</span></span> <span data-ttu-id="82d9a-804">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-804">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-805">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-805">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span><span data-ttu-id="82d9a-806"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | 尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-806"> | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="82d9a-807">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-807">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="82d9a-808">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="82d9a-808">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span><span data-ttu-id="82d9a-809"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="82d9a-809"> | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | The maximum number of concurrent accepts.</span></span> <span data-ttu-id="82d9a-810">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-810">| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="82d9a-811">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="82d9a-811">Use `-1` for infinite.</span></span> <span data-ttu-id="82d9a-812">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-812">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="82d9a-813">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="82d9a-813">(machine-wide</span></span><br><span data-ttu-id="82d9a-814">设置）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="82d9a-814">setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> <span data-ttu-id="82d9a-815">| 30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="82d9a-815">| 30000000 bytes</span></span><br><span data-ttu-id="82d9a-816">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="82d9a-816">(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | The maximum number of requests that can be queued.</span></span> <span data-ttu-id="82d9a-817">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="82d9a-817">| 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="82d9a-818">（正常完成）| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-818">(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="82d9a-819">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="82d9a-819">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="82d9a-820">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-820">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="82d9a-821">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-821">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="82d9a-822">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-822">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="82d9a-823">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-823">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="82d9a-824">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="82d9a-824">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="82d9a-825">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="82d9a-825">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> <span data-ttu-id="82d9a-826">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | 指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="82d9a-826">|  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="82d9a-827">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="82d9a-827">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="82d9a-828">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="82d9a-828">These may be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="82d9a-829">|  |</span><span class="sxs-lookup"><span data-stu-id="82d9a-829">|  |</span></span>

<a name="maxrequestbodysize"></a>

<span data-ttu-id="82d9a-830">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="82d9a-830">**MaxRequestBodySize**</span></span>

<span data-ttu-id="82d9a-831">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-831">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="82d9a-832">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-832">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="82d9a-833">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-833">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="82d9a-834">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="82d9a-834">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="82d9a-835">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="82d9a-835">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="82d9a-836">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="82d9a-836">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="82d9a-837">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="82d9a-837">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="82d9a-838">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="82d9a-838">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="82d9a-839">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="82d9a-839">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="82d9a-840">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="82d9a-840">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="82d9a-842">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="82d9a-842">Configure Windows Server</span></span>

1. <span data-ttu-id="82d9a-843">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="82d9a-843">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="82d9a-844">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-844">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-845">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-845">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="82d9a-846">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="82d9a-846">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="82d9a-847">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-847">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="82d9a-848">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-848">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="82d9a-849">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-849">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="82d9a-850">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="82d9a-850">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="82d9a-851">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-851">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="82d9a-852">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-852">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="82d9a-853">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="82d9a-853">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="82d9a-854">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-854">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="82d9a-855">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="82d9a-855">Install the required .NET Framework.</span></span> <span data-ttu-id="82d9a-856">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="82d9a-856">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="82d9a-857">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="82d9a-857">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="82d9a-858">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="82d9a-858">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="82d9a-859">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-859">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="82d9a-860">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-860">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="82d9a-861">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="82d9a-861">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="82d9a-862">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="82d9a-862">`urls` command-line argument</span></span>
   * <span data-ttu-id="82d9a-863">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="82d9a-863">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="82d9a-864">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-864">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="82d9a-865">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="82d9a-865">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="82d9a-866">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-866">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="82d9a-867">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="82d9a-867">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="82d9a-868">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-868">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="82d9a-869">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="82d9a-869">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="82d9a-870">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="82d9a-870">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="82d9a-871">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-871">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="82d9a-872">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="82d9a-872">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="82d9a-873">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="82d9a-873">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="82d9a-874">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-874">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="82d9a-875">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="82d9a-875">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="82d9a-876">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="82d9a-876">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="82d9a-877">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-877">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="82d9a-878">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="82d9a-878">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="82d9a-879">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="82d9a-879">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="82d9a-880">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-880">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="82d9a-881">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-881">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-882">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-882">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="82d9a-883">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="82d9a-883">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="82d9a-884">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="82d9a-884">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="82d9a-885">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-885">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="82d9a-886">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-886">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="82d9a-887">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-887">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="82d9a-888">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-888">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="82d9a-889">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="82d9a-889">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="82d9a-890">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-890">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="82d9a-891">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="82d9a-891">Don't use a wildcard binding.</span></span> <span data-ttu-id="82d9a-892">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="82d9a-892">Use a valid IP address.</span></span>
   * <span data-ttu-id="82d9a-893">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="82d9a-893">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="82d9a-894">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="82d9a-894">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="82d9a-895">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="82d9a-895">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="82d9a-896">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-896">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="82d9a-897">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-897">In Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-898">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="82d9a-898">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="82d9a-899">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="82d9a-899">Select the **Package** tab.</span></span>
     * <span data-ttu-id="82d9a-900">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="82d9a-900">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="82d9a-901">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="82d9a-901">When not using Visual Studio:</span></span>
     * <span data-ttu-id="82d9a-902">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="82d9a-902">Open the app's project file.</span></span>
     * <span data-ttu-id="82d9a-903">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="82d9a-903">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="82d9a-904">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="82d9a-904">In the following example:</span></span>

   * <span data-ttu-id="82d9a-905">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-905">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="82d9a-906">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="82d9a-906">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="82d9a-907">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="82d9a-907">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="82d9a-908">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="82d9a-908">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="82d9a-909">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="82d9a-909">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="82d9a-910">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="82d9a-910">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)</span></span>
   * <span data-ttu-id="82d9a-911">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="82d9a-911">[UrlPrefix Strings](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)</span></span>

1. <span data-ttu-id="82d9a-912">运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-912">Run the app.</span></span>

   <span data-ttu-id="82d9a-913">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-913">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="82d9a-914">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="82d9a-914">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="82d9a-915">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="82d9a-915">The app responds at the server's public IP address.</span></span> <span data-ttu-id="82d9a-916">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="82d9a-916">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="82d9a-917">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="82d9a-917">A development certificate is used in this example.</span></span> <span data-ttu-id="82d9a-918">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="82d9a-918">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="82d9a-920">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="82d9a-920">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="82d9a-921">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="82d9a-921">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="82d9a-922">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="82d9a-922">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="82d9a-923">其他资源</span><span class="sxs-lookup"><span data-stu-id="82d9a-923">Additional resources</span></span>

* [<span data-ttu-id="82d9a-924">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="82d9a-924">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="82d9a-925">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="82d9a-925">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="82d9a-926">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="82d9a-926">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="82d9a-927">主机</span><span class="sxs-lookup"><span data-stu-id="82d9a-927">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
