---
title: ASP.NET Core 中的会话和应用状态
author: rick-anderson
description: 发现保留请求间会话和应用状态的方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2019
uid: fundamentals/app-state
ms.openlocfilehash: ccb37a422d972ab9113bb4115473d054282dac87
ms.sourcegitcommit: 994da92edb0abf856b1655c18880028b15a28897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71278692"
---
# <a name="session-and-app-state-in-aspnet-core"></a><span data-ttu-id="492d5-103">ASP.NET Core 中的会话和应用状态</span><span class="sxs-lookup"><span data-stu-id="492d5-103">Session and app state in ASP.NET Core</span></span>

<span data-ttu-id="492d5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)、[Diana LaRose](https://github.com/DianaLaRose) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="492d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="492d5-105">HTTP 是无状态的协议。</span><span class="sxs-lookup"><span data-stu-id="492d5-105">HTTP is a stateless protocol.</span></span> <span data-ttu-id="492d5-106">不采取其他步骤的情况下，HTTP 请求是不保留用户值或应用状态的独立消息。</span><span class="sxs-lookup"><span data-stu-id="492d5-106">Without taking additional steps, HTTP requests are independent messages that don't retain user values or app state.</span></span> <span data-ttu-id="492d5-107">本文介绍了几种保留请求间用户数据和应用状态的方法。</span><span class="sxs-lookup"><span data-stu-id="492d5-107">This article describes several approaches to preserve user data and app state between requests.</span></span>

<span data-ttu-id="492d5-108">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="492d5-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="state-management"></a><span data-ttu-id="492d5-109">状态管理</span><span class="sxs-lookup"><span data-stu-id="492d5-109">State management</span></span>

<span data-ttu-id="492d5-110">可以使用几种方法存储状态。</span><span class="sxs-lookup"><span data-stu-id="492d5-110">State can be stored using several approaches.</span></span> <span data-ttu-id="492d5-111">本主题稍后将对每个方法进行介绍。</span><span class="sxs-lookup"><span data-stu-id="492d5-111">Each approach is described later in this topic.</span></span>

| <span data-ttu-id="492d5-112">存储方法</span><span class="sxs-lookup"><span data-stu-id="492d5-112">Storage approach</span></span> | <span data-ttu-id="492d5-113">存储机制</span><span class="sxs-lookup"><span data-stu-id="492d5-113">Storage mechanism</span></span> |
| ---------------- | ----------------- |
| [<span data-ttu-id="492d5-114">Cookie</span><span class="sxs-lookup"><span data-stu-id="492d5-114">Cookies</span></span>](#cookies) | <span data-ttu-id="492d5-115">HTTP Cookie（可能包括使用服务器端应用代码存储的数据）</span><span class="sxs-lookup"><span data-stu-id="492d5-115">HTTP cookies (may include data stored using server-side app code)</span></span> |
| [<span data-ttu-id="492d5-116">Session State</span><span class="sxs-lookup"><span data-stu-id="492d5-116">Session state</span></span>](#session-state) | <span data-ttu-id="492d5-117">HTTP Cookie 和服务器端应用代码</span><span class="sxs-lookup"><span data-stu-id="492d5-117">HTTP cookies and server-side app code</span></span> |
| [<span data-ttu-id="492d5-118">TempData</span><span class="sxs-lookup"><span data-stu-id="492d5-118">TempData</span></span>](#tempdata) | <span data-ttu-id="492d5-119">HTTP Cookie 或会话状态</span><span class="sxs-lookup"><span data-stu-id="492d5-119">HTTP cookies or session state</span></span> |
| [<span data-ttu-id="492d5-120">Query Strings</span><span class="sxs-lookup"><span data-stu-id="492d5-120">Query strings</span></span>](#query-strings) | <span data-ttu-id="492d5-121">HTTP 查询字符串</span><span class="sxs-lookup"><span data-stu-id="492d5-121">HTTP query strings</span></span> |
| [<span data-ttu-id="492d5-122">Hidden Fields</span><span class="sxs-lookup"><span data-stu-id="492d5-122">Hidden fields</span></span>](#hidden-fields) | <span data-ttu-id="492d5-123">HTTP 窗体字段</span><span class="sxs-lookup"><span data-stu-id="492d5-123">HTTP form fields</span></span> |
| [<span data-ttu-id="492d5-124">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="492d5-124">HttpContext.Items</span></span>](#httpcontextitems) | <span data-ttu-id="492d5-125">服务器端应用代码</span><span class="sxs-lookup"><span data-stu-id="492d5-125">Server-side app code</span></span> |
| [<span data-ttu-id="492d5-126">Cache</span><span class="sxs-lookup"><span data-stu-id="492d5-126">Cache</span></span>](#cache) | <span data-ttu-id="492d5-127">服务器端应用代码</span><span class="sxs-lookup"><span data-stu-id="492d5-127">Server-side app code</span></span> |
| [<span data-ttu-id="492d5-128">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="492d5-128">Dependency Injection</span></span>](#dependency-injection) | <span data-ttu-id="492d5-129">服务器端应用代码</span><span class="sxs-lookup"><span data-stu-id="492d5-129">Server-side app code</span></span> |

## <a name="cookies"></a><span data-ttu-id="492d5-130">Cookie</span><span class="sxs-lookup"><span data-stu-id="492d5-130">Cookies</span></span>

<span data-ttu-id="492d5-131">Cookie 存储所有请求的数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-131">Cookies store data across requests.</span></span> <span data-ttu-id="492d5-132">因为 Cookie 是随每个请求发送的，所以它们的大小应该保持在最低限度。</span><span class="sxs-lookup"><span data-stu-id="492d5-132">Because cookies are sent with every request, their size should be kept to a minimum.</span></span> <span data-ttu-id="492d5-133">理想情况下，仅标识符应存储在 Cookie 中，而数据则由应用存储。</span><span class="sxs-lookup"><span data-stu-id="492d5-133">Ideally, only an identifier should be stored in a cookie with the data stored by the app.</span></span> <span data-ttu-id="492d5-134">大多数浏览器 Cookie 大小限制为 4096 个字节。</span><span class="sxs-lookup"><span data-stu-id="492d5-134">Most browsers restrict cookie size to 4096 bytes.</span></span> <span data-ttu-id="492d5-135">每个域仅有有限数量的 Cookie 可用。</span><span class="sxs-lookup"><span data-stu-id="492d5-135">Only a limited number of cookies are available for each domain.</span></span>

<span data-ttu-id="492d5-136">由于 Cookie 易被篡改，因此它们必须由服务器进行验证。</span><span class="sxs-lookup"><span data-stu-id="492d5-136">Because cookies are subject to tampering, they must be validated by the app.</span></span> <span data-ttu-id="492d5-137">客户端上的 Cookie 可能被用户删除或者过期。</span><span class="sxs-lookup"><span data-stu-id="492d5-137">Cookies can be deleted by users and expire on clients.</span></span> <span data-ttu-id="492d5-138">但是，Cookie 通常是客户端上最持久的数据暂留形式。</span><span class="sxs-lookup"><span data-stu-id="492d5-138">However, cookies are generally the most durable form of data persistence on the client.</span></span>

<span data-ttu-id="492d5-139">Cookie 通常用于个性化设置，其中的内容是为已知用户定制的。</span><span class="sxs-lookup"><span data-stu-id="492d5-139">Cookies are often used for personalization, where content is customized for a known user.</span></span> <span data-ttu-id="492d5-140">大多数情况下，仅标识用户，但不对其进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="492d5-140">The user is only identified and not authenticated in most cases.</span></span> <span data-ttu-id="492d5-141">Cookie 可以存储用户名、帐户名或唯一的用户 ID（例如 GUID）。</span><span class="sxs-lookup"><span data-stu-id="492d5-141">The cookie can store the user's name, account name, or unique user ID (such as a GUID).</span></span> <span data-ttu-id="492d5-142">然后，可以使用 Cookie 访问用户的个性化设置，例如首选的网站背景色。</span><span class="sxs-lookup"><span data-stu-id="492d5-142">You can then use the cookie to access the user's personalized settings, such as their preferred website background color.</span></span>

<span data-ttu-id="492d5-143">发布 Cookie 和处理隐私问题时，请留意[欧盟一般数据保护条例 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection)。</span><span class="sxs-lookup"><span data-stu-id="492d5-143">Be mindful of the [European Union General Data Protection Regulations (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) when issuing cookies and dealing with privacy concerns.</span></span> <span data-ttu-id="492d5-144">有关详细信息，请参阅 [ASP.NET Core 中的一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="492d5-144">For more information, see [General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr).</span></span>

## <a name="session-state"></a><span data-ttu-id="492d5-145">会话状态</span><span class="sxs-lookup"><span data-stu-id="492d5-145">Session state</span></span>

<span data-ttu-id="492d5-146">会话状态是在用户浏览 Web 应用时用来存储用户数据的 ASP.NET Core 方案。</span><span class="sxs-lookup"><span data-stu-id="492d5-146">Session state is an ASP.NET Core scenario for storage of user data while the user browses a web app.</span></span> <span data-ttu-id="492d5-147">会话状态使用应用维护的存储来保存客户端所有请求的数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-147">Session state uses a store maintained by the app to persist data across requests from a client.</span></span> <span data-ttu-id="492d5-148">会话数据由缓存支持并被视为临时数据 - 站点应在没有会话数据的情况下继续运行。</span><span class="sxs-lookup"><span data-stu-id="492d5-148">The session data is backed by a cache and considered ephemeral data&mdash;the site should continue to function without the session data.</span></span> <span data-ttu-id="492d5-149">关键应用程序数据应存储在用户数据库中，并仅作为性能优化缓存在会话中。</span><span class="sxs-lookup"><span data-stu-id="492d5-149">Critical application data should be stored in the user database and cached in session only as a performance optimization.</span></span>

> [!NOTE]
> <span data-ttu-id="492d5-150">[SignalR](xref:signalr/index) 应用不支持会话，因为 [SignalR 中心](xref:signalr/hubs)可能独立于 HTTP 上下文执行。</span><span class="sxs-lookup"><span data-stu-id="492d5-150">Session isn't supported in [SignalR](xref:signalr/index) apps because a [SignalR Hub](xref:signalr/hubs) may execute independent of an HTTP context.</span></span> <span data-ttu-id="492d5-151">例如，当中心打开的长轮询请求超出请求的 HTTP 上下文的生存期时，可能发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="492d5-151">For example, this can occur when a long polling request is held open by a hub beyond the lifetime of the request's HTTP context.</span></span>

<span data-ttu-id="492d5-152">ASP.NET Core 通过向客户端提供包含会话 ID 的 Cookie 来维护会话状态，该会话 ID 与每个请求一起发送到应用。</span><span class="sxs-lookup"><span data-stu-id="492d5-152">ASP.NET Core maintains session state by providing a cookie to the client that contains a session ID, which is sent to the app with each request.</span></span> <span data-ttu-id="492d5-153">应用使用会话 ID 来获取会话数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-153">The app uses the session ID to fetch the session data.</span></span>

<span data-ttu-id="492d5-154">会话状态具有以下行为：</span><span class="sxs-lookup"><span data-stu-id="492d5-154">Session state exhibits the following behaviors:</span></span>

* <span data-ttu-id="492d5-155">由于会话 Cookie 是特定于浏览器的，因此不能跨浏览器共享会话。</span><span class="sxs-lookup"><span data-stu-id="492d5-155">Because the session cookie is specific to the browser, sessions aren't shared across browsers.</span></span>
* <span data-ttu-id="492d5-156">浏览器会话结束时删除会话 Cookie。</span><span class="sxs-lookup"><span data-stu-id="492d5-156">Session cookies are deleted when the browser session ends.</span></span>
* <span data-ttu-id="492d5-157">如果收到过期的会话 Cookie，则创建使用相同会话 Cookie 的新会话。</span><span class="sxs-lookup"><span data-stu-id="492d5-157">If a cookie is received for an expired session, a new session is created that uses the same session cookie.</span></span>
* <span data-ttu-id="492d5-158">不会保留空会话 - 会话中必须设置了至少一个值以保存所有请求的会话。</span><span class="sxs-lookup"><span data-stu-id="492d5-158">Empty sessions aren't retained&mdash;the session must have at least one value set into it to persist the session across requests.</span></span> <span data-ttu-id="492d5-159">会话未保留时，为每个新的请求生成新会话 ID。</span><span class="sxs-lookup"><span data-stu-id="492d5-159">When a session isn't retained, a new session ID is generated for each new request.</span></span>
* <span data-ttu-id="492d5-160">应用在上次请求后保留会话的时间有限。</span><span class="sxs-lookup"><span data-stu-id="492d5-160">The app retains a session for a limited time after the last request.</span></span> <span data-ttu-id="492d5-161">应用设置会话超时，或者使用 20 分钟的默认值。</span><span class="sxs-lookup"><span data-stu-id="492d5-161">The app either sets the session timeout or uses the default value of 20 minutes.</span></span> <span data-ttu-id="492d5-162">会话状态适用于存储特定于特定会话的用户数据，但该数据无需永久的会话存储。</span><span class="sxs-lookup"><span data-stu-id="492d5-162">Session state is ideal for storing user data that's specific to a particular session but where the data doesn't require permanent storage across sessions.</span></span>
* <span data-ttu-id="492d5-163">调用 [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) 实现或者会话过期时，会删除会话数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-163">Session data is deleted either when the [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) implementation is called or when the session expires.</span></span>
* <span data-ttu-id="492d5-164">没有默认机制告知客户端浏览器已关闭或者客户端上的会话 Cookie 被删除或过期的应用代码。</span><span class="sxs-lookup"><span data-stu-id="492d5-164">There's no default mechanism to inform app code that a client browser has been closed or when the session cookie is deleted or expired on the client.</span></span>
* <span data-ttu-id="492d5-165">ASP.NET Core MVC 和 Razor Pages 模板包括对一般数据保护条例 (GDPR) 的支持。</span><span class="sxs-lookup"><span data-stu-id="492d5-165">The ASP.NET Core MVC and Razor pages templates include support for General Data Protection Regulation (GDPR).</span></span> <span data-ttu-id="492d5-166">默认情况下，会话状态 cookie 不标记为“基本”，因此，除非站点访问者允许跟踪，否则会话状态不起作用。</span><span class="sxs-lookup"><span data-stu-id="492d5-166">Session state cookies aren't marked essential by default, so session state isn't functional unless tracking is permitted by the site visitor.</span></span> <span data-ttu-id="492d5-167">有关详细信息，请参阅 <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>。</span><span class="sxs-lookup"><span data-stu-id="492d5-167">For more information, see <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.</span></span>

> [!WARNING]
> <span data-ttu-id="492d5-168">请勿将敏感数据存储在会话状态中。</span><span class="sxs-lookup"><span data-stu-id="492d5-168">Don't store sensitive data in session state.</span></span> <span data-ttu-id="492d5-169">用户可能不会关闭浏览器并清除会话 Cookie。</span><span class="sxs-lookup"><span data-stu-id="492d5-169">The user might not close the browser and clear the session cookie.</span></span> <span data-ttu-id="492d5-170">某些浏览器会保留所有浏览器窗口中的有效会话 Cookie。</span><span class="sxs-lookup"><span data-stu-id="492d5-170">Some browsers maintain valid session cookies across browser windows.</span></span> <span data-ttu-id="492d5-171">会话可能不限于单个用户 - 下一个用户可能继续使用同一会话 Cookie 浏览应用。</span><span class="sxs-lookup"><span data-stu-id="492d5-171">A session might not be restricted to a single user&mdash;the next user might continue to browse the app with the same session cookie.</span></span>

<span data-ttu-id="492d5-172">内存中缓存提供程序在应用驻留的服务器内存中存储会话数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-172">The in-memory cache provider stores session data in the memory of the server where the app resides.</span></span> <span data-ttu-id="492d5-173">在服务器场方案中：</span><span class="sxs-lookup"><span data-stu-id="492d5-173">In a server farm scenario:</span></span>

* <span data-ttu-id="492d5-174">使用粘性会话将每个会话加入到单独服务器上的特定应用实例  。</span><span class="sxs-lookup"><span data-stu-id="492d5-174">Use *sticky sessions* to tie each session to a specific app instance on an individual server.</span></span> <span data-ttu-id="492d5-175">默认情况下，[Azure 应用服务](https://azure.microsoft.com/services/app-service/)使用[应用程序请求路由 (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 强制实施粘性会话。</span><span class="sxs-lookup"><span data-stu-id="492d5-175">[Azure App Service](https://azure.microsoft.com/services/app-service/) uses [Application Request Routing (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) to enforce sticky sessions by default.</span></span> <span data-ttu-id="492d5-176">然而，粘性会话可能会影响可伸缩性，并使 Web 应用更新变得复杂。</span><span class="sxs-lookup"><span data-stu-id="492d5-176">However, sticky sessions can affect scalability and complicate web app updates.</span></span> <span data-ttu-id="492d5-177">更好的方法是使用 Redis 或 SQL Server 分布式缓存，它们不需要粘性会话。</span><span class="sxs-lookup"><span data-stu-id="492d5-177">A better approach is to use a Redis or SQL Server distributed cache, which doesn't require sticky sessions.</span></span> <span data-ttu-id="492d5-178">有关详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="492d5-178">For more information, see <xref:performance/caching/distributed>.</span></span>
* <span data-ttu-id="492d5-179">通过 [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) 加密会话 Cookie。</span><span class="sxs-lookup"><span data-stu-id="492d5-179">The session cookie is encrypted via [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector).</span></span> <span data-ttu-id="492d5-180">必须正确配置数据保护，以在每台计算机上读取会话 Cookie。</span><span class="sxs-lookup"><span data-stu-id="492d5-180">Data Protection must be properly configured to read session cookies on each machine.</span></span> <span data-ttu-id="492d5-181">有关详细信息，请参阅 <xref:security/data-protection/introduction> 和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)。</span><span class="sxs-lookup"><span data-stu-id="492d5-181">For more information, see <xref:security/data-protection/introduction> and [Key storage providers](xref:security/data-protection/implementation/key-storage-providers).</span></span>

### <a name="configure-session-state"></a><span data-ttu-id="492d5-182">配置会话状态</span><span class="sxs-lookup"><span data-stu-id="492d5-182">Configure session state</span></span>

<span data-ttu-id="492d5-183">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含的 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包提供中间件来管理会话状态。</span><span class="sxs-lookup"><span data-stu-id="492d5-183">The [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), provides middleware for managing session state.</span></span> <span data-ttu-id="492d5-184">若要启用会话中间件，`Startup` 必须包含：</span><span class="sxs-lookup"><span data-stu-id="492d5-184">To enable the session middleware, `Startup` must contain:</span></span>

* <span data-ttu-id="492d5-185">任一 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) 内存缓存。</span><span class="sxs-lookup"><span data-stu-id="492d5-185">Any of the [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) memory caches.</span></span> <span data-ttu-id="492d5-186">`IDistributedCache` 实现用作会话后备存储。</span><span class="sxs-lookup"><span data-stu-id="492d5-186">The `IDistributedCache` implementation is used as a backing store for session.</span></span> <span data-ttu-id="492d5-187">有关详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="492d5-187">For more information, see <xref:performance/caching/distributed>.</span></span>
* <span data-ttu-id="492d5-188">对 `ConfigureServices` 中 [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) 的调用。</span><span class="sxs-lookup"><span data-stu-id="492d5-188">A call to [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) in `ConfigureServices`.</span></span>
* <span data-ttu-id="492d5-189">对 `Configure` 中 [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 的调用。</span><span class="sxs-lookup"><span data-stu-id="492d5-189">A call to [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in `Configure`.</span></span>

<span data-ttu-id="492d5-190">以下代码演示如何使用 `IDistributedCache` 的默认内存中实现设置内存中会话提供程序：</span><span class="sxs-lookup"><span data-stu-id="492d5-190">The following code shows how to set up the in-memory session provider with a default in-memory implementation of `IDistributedCache`:</span></span>

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

<span data-ttu-id="492d5-191">中间件的顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="492d5-191">The order of middleware is important.</span></span> <span data-ttu-id="492d5-192">在前面的示例中，在 `UseMvc` 之后调用 `UseSession` 时会发生 `InvalidOperationException` 异常。</span><span class="sxs-lookup"><span data-stu-id="492d5-192">In the preceding example, an `InvalidOperationException` exception occurs when `UseSession` is invoked after `UseMvc`.</span></span> <span data-ttu-id="492d5-193">有关详细信息，请参阅[中间件排序](xref:fundamentals/middleware/index#order)。</span><span class="sxs-lookup"><span data-stu-id="492d5-193">For more information, see [Middleware Ordering](xref:fundamentals/middleware/index#order).</span></span>

<span data-ttu-id="492d5-194">配置会话状态后，[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) 可用。</span><span class="sxs-lookup"><span data-stu-id="492d5-194">[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) is available after session state is configured.</span></span>

<span data-ttu-id="492d5-195">调用 `UseSession` 以前无法访问 `HttpContext.Session`。</span><span class="sxs-lookup"><span data-stu-id="492d5-195">`HttpContext.Session` can't be accessed before `UseSession` has been called.</span></span>

<span data-ttu-id="492d5-196">在应用已经开始写入到响应流之后，不能创建有新会话 Cookie 的新会话。</span><span class="sxs-lookup"><span data-stu-id="492d5-196">A new session with a new session cookie can't be created after the app has begun writing to the response stream.</span></span> <span data-ttu-id="492d5-197">此异常记录在 Web 服务器日志中但不显示在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="492d5-197">The exception is recorded in the web server log and not displayed in the browser.</span></span>

### <a name="load-session-state-asynchronously"></a><span data-ttu-id="492d5-198">以异步方式加载会话状态</span><span class="sxs-lookup"><span data-stu-id="492d5-198">Load session state asynchronously</span></span>

<span data-ttu-id="492d5-199">只有在 [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue)、[Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) 或 [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) 方法之前显式调用 [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) 方法，ASP.NET Core 中的默认会话提供程序才会从基础 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) 后备存储以异步方式加载会话记录。</span><span class="sxs-lookup"><span data-stu-id="492d5-199">The default session provider in ASP.NET Core loads session records from the underlying [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) backing store asynchronously only if the [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) method is explicitly called before the [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [Set](/dotnet/api/microsoft.aspnetcore.http.isession.set), or [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) methods.</span></span> <span data-ttu-id="492d5-200">如果未先调用 `LoadAsync`，则会同步加载基础会话记录，这可能对性能产生大规模影响。</span><span class="sxs-lookup"><span data-stu-id="492d5-200">If `LoadAsync` isn't called first, the underlying session record is loaded synchronously, which can incur a performance penalty at scale.</span></span>

<span data-ttu-id="492d5-201">若要让应用强制实施此模式，如果未在 `TryGetValue`、`Set` 或 `Remove` 之前调用 `LoadAsync` 方法，那么使用引起异常的版本包装 [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) 和 [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) 实现。</span><span class="sxs-lookup"><span data-stu-id="492d5-201">To have apps enforce this pattern, wrap the [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) and [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) implementations with versions that throw an exception if the `LoadAsync` method isn't called before `TryGetValue`, `Set`, or `Remove`.</span></span> <span data-ttu-id="492d5-202">在服务容器中注册的已包装的版本。</span><span class="sxs-lookup"><span data-stu-id="492d5-202">Register the wrapped versions in the services container.</span></span>

### <a name="session-options"></a><span data-ttu-id="492d5-203">会话选项</span><span class="sxs-lookup"><span data-stu-id="492d5-203">Session options</span></span>

<span data-ttu-id="492d5-204">若要替代会话默认值，请使用 [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions)。</span><span class="sxs-lookup"><span data-stu-id="492d5-204">To override session defaults, use [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions).</span></span>

| <span data-ttu-id="492d5-205">选项</span><span class="sxs-lookup"><span data-stu-id="492d5-205">Option</span></span> | <span data-ttu-id="492d5-206">说明</span><span class="sxs-lookup"><span data-stu-id="492d5-206">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="492d5-207">Cookie</span><span class="sxs-lookup"><span data-stu-id="492d5-207">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | <span data-ttu-id="492d5-208">确定用于创建 Cookie 的设置。</span><span class="sxs-lookup"><span data-stu-id="492d5-208">Determines the settings used to create the cookie.</span></span> <span data-ttu-id="492d5-209">[名称](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name)默认为 [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`)。</span><span class="sxs-lookup"><span data-stu-id="492d5-209">[Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) defaults to [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`).</span></span> <span data-ttu-id="492d5-210">[路径](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path)默认为 [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`)。</span><span class="sxs-lookup"><span data-stu-id="492d5-210">[Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) defaults to [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`).</span></span> <span data-ttu-id="492d5-211">[SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) 默认为 [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`)。</span><span class="sxs-lookup"><span data-stu-id="492d5-211">[SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) defaults to [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`).</span></span> <span data-ttu-id="492d5-212">[HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) 默认为 `true`。</span><span class="sxs-lookup"><span data-stu-id="492d5-212">[HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) defaults to `true`.</span></span> <span data-ttu-id="492d5-213">[IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) 默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="492d5-213">[IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) defaults to `false`.</span></span> |
| [<span data-ttu-id="492d5-214">IdleTimeout</span><span class="sxs-lookup"><span data-stu-id="492d5-214">IdleTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | <span data-ttu-id="492d5-215">`IdleTimeout` 显示放弃其内容前，内容可以空闲多长时间。</span><span class="sxs-lookup"><span data-stu-id="492d5-215">The `IdleTimeout` indicates how long the session can be idle before its contents are abandoned.</span></span> <span data-ttu-id="492d5-216">每个会话访问都会重置超时。</span><span class="sxs-lookup"><span data-stu-id="492d5-216">Each session access resets the timeout.</span></span> <span data-ttu-id="492d5-217">此设置仅适用于会话内容，不适用于 Cookie。</span><span class="sxs-lookup"><span data-stu-id="492d5-217">This setting only applies to the content of the session, not the cookie.</span></span> <span data-ttu-id="492d5-218">默认为 20 分钟。</span><span class="sxs-lookup"><span data-stu-id="492d5-218">The default is 20 minutes.</span></span> |
| [<span data-ttu-id="492d5-219">IOTimeout</span><span class="sxs-lookup"><span data-stu-id="492d5-219">IOTimeout</span></span>](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | <span data-ttu-id="492d5-220">允许从存储加载会话或者将其提交回存储的最大时长。</span><span class="sxs-lookup"><span data-stu-id="492d5-220">The maximum amount of time allowed to load a session from the store or to commit it back to the store.</span></span> <span data-ttu-id="492d5-221">此设置可能仅适用于异步操作。</span><span class="sxs-lookup"><span data-stu-id="492d5-221">This setting may only apply to asynchronous operations.</span></span> <span data-ttu-id="492d5-222">可以使用 [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan) 禁用超时。</span><span class="sxs-lookup"><span data-stu-id="492d5-222">This timeout can be disabled using [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan).</span></span> <span data-ttu-id="492d5-223">默认值为 1 分钟。</span><span class="sxs-lookup"><span data-stu-id="492d5-223">The default is 1 minute.</span></span> |

<span data-ttu-id="492d5-224">会话使用 Cookie 跟踪和标识来自单个浏览器的请求。</span><span class="sxs-lookup"><span data-stu-id="492d5-224">Session uses a cookie to track and identify requests from a single browser.</span></span> <span data-ttu-id="492d5-225">默认情况下，此 Cookie 名为 `.AspNetCore.Session` ，并使用路径 `/`。</span><span class="sxs-lookup"><span data-stu-id="492d5-225">By default, this cookie is named `.AspNetCore.Session`, and it uses a path of `/`.</span></span> <span data-ttu-id="492d5-226">由于 Cookie 默认值不指定域，因此它不提供页上的客户端脚本（因为 [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) 默认为 `true`）。</span><span class="sxs-lookup"><span data-stu-id="492d5-226">Because the cookie default doesn't specify a domain, it isn't made available to the client-side script on the page (because [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) defaults to `true`).</span></span>

<span data-ttu-id="492d5-227">若要替换 Cookie 会话默认值，请使用 `SessionOptions`：</span><span class="sxs-lookup"><span data-stu-id="492d5-227">To override cookie session defaults, use `SessionOptions`:</span></span>

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

<span data-ttu-id="492d5-228">应用使用 [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) 属性确定放弃服务器缓存中的内容前，内容可以空闲多长时间。</span><span class="sxs-lookup"><span data-stu-id="492d5-228">The app uses the [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) property to determine how long a session can be idle before its contents in the server's cache are abandoned.</span></span> <span data-ttu-id="492d5-229">此属性独立于 Cookie 到期时间。</span><span class="sxs-lookup"><span data-stu-id="492d5-229">This property is independent of the cookie expiration.</span></span> <span data-ttu-id="492d5-230">通过[会话中间件](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware)传递的每个请求都会重置超时。</span><span class="sxs-lookup"><span data-stu-id="492d5-230">Each request that passes through the [Session Middleware](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) resets the timeout.</span></span>

<span data-ttu-id="492d5-231">会话状态为“非锁定”  。</span><span class="sxs-lookup"><span data-stu-id="492d5-231">Session state is *non-locking*.</span></span> <span data-ttu-id="492d5-232">如果两个请求同时尝试修改同一会话的内容，则后一个请求替代前一个请求。</span><span class="sxs-lookup"><span data-stu-id="492d5-232">If two requests simultaneously attempt to modify the contents of a session, the last request overrides the first.</span></span> <span data-ttu-id="492d5-233">`Session` 是作为一个连贯会话实现的，这意味着所有内容都存储在一起  。</span><span class="sxs-lookup"><span data-stu-id="492d5-233">`Session` is implemented as a *coherent session*, which means that all the contents are stored together.</span></span> <span data-ttu-id="492d5-234">两个请求试图修改不同的会话值时，后一个请求可能替代前一个做出的会话更改。</span><span class="sxs-lookup"><span data-stu-id="492d5-234">When two requests seek to modify different session values, the last request may override session changes made by the first.</span></span>

### <a name="set-and-get-session-values"></a><span data-ttu-id="492d5-235">设置和获取会话值</span><span class="sxs-lookup"><span data-stu-id="492d5-235">Set and get Session values</span></span>

<span data-ttu-id="492d5-236">使用 [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) 从 Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 类或 MVC [控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)类访问会话状态。</span><span class="sxs-lookup"><span data-stu-id="492d5-236">Session state is accessed from a Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) class or MVC [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) class with [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session).</span></span> <span data-ttu-id="492d5-237">此属性是 [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) 实现。</span><span class="sxs-lookup"><span data-stu-id="492d5-237">This property is an [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) implementation.</span></span>

<span data-ttu-id="492d5-238">`ISession` 实现提供用于设置和检索整数和字符串值的若干扩展方法。</span><span class="sxs-lookup"><span data-stu-id="492d5-238">The `ISession` implementation provides several extension methods to set and retrieve integer and string values.</span></span> <span data-ttu-id="492d5-239">项目引用 [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) 包时，扩展方法位于 [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) 命名空间中（添加 `using Microsoft.AspNetCore.Http;` 语句获取对扩展方法的访问权限）。</span><span class="sxs-lookup"><span data-stu-id="492d5-239">The extension methods are in the [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) namespace (add a `using Microsoft.AspNetCore.Http;` statement to gain access to the extension methods) when the [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) package is referenced by the project.</span></span> <span data-ttu-id="492d5-240">这两个包均包括在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="492d5-240">Both packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="492d5-241">`ISession` 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="492d5-241">`ISession` extension methods:</span></span>

* [<span data-ttu-id="492d5-242">Get(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="492d5-242">Get(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [<span data-ttu-id="492d5-243">GetInt32(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="492d5-243">GetInt32(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [<span data-ttu-id="492d5-244">GetString(ISession, String)</span><span class="sxs-lookup"><span data-stu-id="492d5-244">GetString(ISession, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [<span data-ttu-id="492d5-245">SetInt32(ISession, String, Int32)</span><span class="sxs-lookup"><span data-stu-id="492d5-245">SetInt32(ISession, String, Int32)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [<span data-ttu-id="492d5-246">SetString(ISession, String, String)</span><span class="sxs-lookup"><span data-stu-id="492d5-246">SetString(ISession, String, String)</span></span>](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

<span data-ttu-id="492d5-247">以下示例在 Razor Pages 页中检索 `IndexModel.SessionKeyName` 键（示例应用中的 `_Name`）的会话值：</span><span class="sxs-lookup"><span data-stu-id="492d5-247">The following example retrieves the session value for the `IndexModel.SessionKeyName` key (`_Name` in the sample app) in a Razor Pages page:</span></span>

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

<span data-ttu-id="492d5-248">以下示例显示如何设置和获取整数和字符串：</span><span class="sxs-lookup"><span data-stu-id="492d5-248">The following example shows how to set and get an integer and a string:</span></span>

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

<span data-ttu-id="492d5-249">必须对所有会话数据进行序列化以启用分布式缓存方案，即使是在使用内存中缓存的时候。</span><span class="sxs-lookup"><span data-stu-id="492d5-249">All session data must be serialized to enable a distributed cache scenario, even when using the in-memory cache.</span></span> <span data-ttu-id="492d5-250">提供最小的字符串和数字序列化程序（请参阅 [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) 的方法和扩展方法）。</span><span class="sxs-lookup"><span data-stu-id="492d5-250">Minimal string and number serializers are provided (see the methods and extension methods of [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)).</span></span> <span data-ttu-id="492d5-251">用户必须使用另一种机制（例如 JSON）序列化复杂类型。</span><span class="sxs-lookup"><span data-stu-id="492d5-251">Complex types must be serialized by the user using another mechanism, such as JSON.</span></span>

<span data-ttu-id="492d5-252">添加以下扩展方法以设置和获取可序列化的对象：</span><span class="sxs-lookup"><span data-stu-id="492d5-252">Add the following extension methods to set and get serializable objects:</span></span>

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

<span data-ttu-id="492d5-253">以下示例演示如何使用扩展方法设置和获取可序列化的对象：</span><span class="sxs-lookup"><span data-stu-id="492d5-253">The following example shows how to set and get a serializable object with the extension methods:</span></span>

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a><span data-ttu-id="492d5-254">TempData</span><span class="sxs-lookup"><span data-stu-id="492d5-254">TempData</span></span>

<span data-ttu-id="492d5-255">ASP.NET Core 公开 Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 或控制器 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>。</span><span class="sxs-lookup"><span data-stu-id="492d5-255">ASP.NET Core exposes the Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) or Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="492d5-256">在另一个请求读取数据之前，此属性将读取此数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-256">This property stores data until it's read in another request.</span></span> <span data-ttu-id="492d5-257">[Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 和 [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) 方法可用于检查数据，而无需在请求结束时删除。</span><span class="sxs-lookup"><span data-stu-id="492d5-257">[Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) and [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) methods can be used to examine the data without deletion at the end of the request.</span></span> <span data-ttu-id="492d5-258">[Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 将标记字典中的所有项以进行保留。</span><span class="sxs-lookup"><span data-stu-id="492d5-258">[Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) marks all items in the dictionary for retention.</span></span> <span data-ttu-id="492d5-259">当多个请求需要数据时，`TempData` 非常有助于进行重定向。</span><span class="sxs-lookup"><span data-stu-id="492d5-259">`TempData` is particularly useful for redirection when data is required for more than a single request.</span></span> <span data-ttu-id="492d5-260">`TempData` 提供程序使用 Cookie 或会话状态实现 `TempData`。</span><span class="sxs-lookup"><span data-stu-id="492d5-260">`TempData` is implemented by `TempData` providers using either cookies or session state.</span></span>

## <a name="tempdata-samples"></a><span data-ttu-id="492d5-261">TempData 示例</span><span class="sxs-lookup"><span data-stu-id="492d5-261">TempData samples</span></span>

<span data-ttu-id="492d5-262">考虑创建客户的以下页面：</span><span class="sxs-lookup"><span data-stu-id="492d5-262">Consider the following page that creates a customer:</span></span>

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

<span data-ttu-id="492d5-263">以下页面显示 `TempData["Message"]`：</span><span class="sxs-lookup"><span data-stu-id="492d5-263">The following page displays `TempData["Message"]`:</span></span>

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

<span data-ttu-id="492d5-264">在前面的标记中，在请求结束时，不会删除 `TempData["Message"]`，因为正在使用 `Peek`。 </span><span class="sxs-lookup"><span data-stu-id="492d5-264">In the preceding markup, at the end of the request, `TempData["Message"]` is **not** deleted because `Peek` is used.</span></span> <span data-ttu-id="492d5-265">刷新页面将显示 `TempData["Message"]`。</span><span class="sxs-lookup"><span data-stu-id="492d5-265">Refreshing the page displays `TempData["Message"]`.</span></span>

<span data-ttu-id="492d5-266">以下标记类似于前面的代码，但使用 `Keep` 在请求结束时保留数据：</span><span class="sxs-lookup"><span data-stu-id="492d5-266">The following markup is similar to the preceding code, but uses `Keep` to preserve the data at the end of the request:</span></span>

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

<span data-ttu-id="492d5-267">在 IndexPeek  和 IndexKeep  页面之间导航不会删除 `TempData["Message"]`。</span><span class="sxs-lookup"><span data-stu-id="492d5-267">Navigating between the *IndexPeek* and *IndexKeep* pages won't delete `TempData["Message"]`.</span></span>

<span data-ttu-id="492d5-268">以下代码显示 `TempData["Message"]`，但请求结束时，将删除 `TempData["Message"]`：</span><span class="sxs-lookup"><span data-stu-id="492d5-268">The following code displays `TempData["Message"]`, but at the end of the request, `TempData["Message"]` is deleted:</span></span>

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a><span data-ttu-id="492d5-269">TempData 提供程序</span><span class="sxs-lookup"><span data-stu-id="492d5-269">TempData providers</span></span>

<span data-ttu-id="492d5-270">基于 cookie 的 TempData 提供程序默认用于存储 cookie 中的 TempData。</span><span class="sxs-lookup"><span data-stu-id="492d5-270">The cookie-based TempData provider is used by default to store TempData in cookies.</span></span>

<span data-ttu-id="492d5-271">使用由 [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder) 编码的 [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) 对 Cookie 数据进行加密，然后进行分块。</span><span class="sxs-lookup"><span data-stu-id="492d5-271">The cookie data is encrypted using [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector), encoded with [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder), then chunked.</span></span> <span data-ttu-id="492d5-272">因为 Cookie 进行了分块，所以 ASP.NET Core 1.x 中的单个 Cookie 大小限制不适用。</span><span class="sxs-lookup"><span data-stu-id="492d5-272">Because the cookie is chunked, the single cookie size limit found in ASP.NET Core 1.x doesn't apply.</span></span> <span data-ttu-id="492d5-273">未压缩 Cookie 数据，因为压缩加密的数据会导致安全问题，如 [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻击。</span><span class="sxs-lookup"><span data-stu-id="492d5-273">The cookie data isn't compressed because compressing encrypted data can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span> <span data-ttu-id="492d5-274">有关基于 Cookie 的 TempData 提供程序的详细信息，请参阅 [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider)。</span><span class="sxs-lookup"><span data-stu-id="492d5-274">For more information on the cookie-based TempData provider, see [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).</span></span>

### <a name="choose-a-tempdata-provider"></a><span data-ttu-id="492d5-275">选择 TempData 提供程序</span><span class="sxs-lookup"><span data-stu-id="492d5-275">Choose a TempData provider</span></span>

<span data-ttu-id="492d5-276">选择 TempData 提供程序涉及几个注意事项，例如：</span><span class="sxs-lookup"><span data-stu-id="492d5-276">Choosing a TempData provider involves several considerations, such as:</span></span>

1. <span data-ttu-id="492d5-277">应用是否已使用会话状态？</span><span class="sxs-lookup"><span data-stu-id="492d5-277">Does the app already use session state?</span></span> <span data-ttu-id="492d5-278">如果是，使用会话状态 TempData 提供程序对应用没有额外的成本（除了数据的大小）。</span><span class="sxs-lookup"><span data-stu-id="492d5-278">If so, using the session state TempData provider has no additional cost to the app (aside from the size of the data).</span></span>
2. <span data-ttu-id="492d5-279">应用是否只对相对较小的数据量（最多 500 个字节）使用 TempData？</span><span class="sxs-lookup"><span data-stu-id="492d5-279">Does the app use TempData only sparingly for relatively small amounts of data (up to 500 bytes)?</span></span> <span data-ttu-id="492d5-280">如果是，Cookie TempData 提供程序将为每个携带 TempData 的请求增加较小的成本。</span><span class="sxs-lookup"><span data-stu-id="492d5-280">If so, the cookie TempData provider adds a small cost to each request that carries TempData.</span></span> <span data-ttu-id="492d5-281">如果不是，会话状态 TempData 提供程序有助于在使用 TempData 前，避免在每个请求中来回切换大量数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-281">If not, the session state TempData provider can be beneficial to avoid round-tripping a large amount of data in each request until the TempData is consumed.</span></span>
3. <span data-ttu-id="492d5-282">应用是否在多个服务器上的服务器场中运行？</span><span class="sxs-lookup"><span data-stu-id="492d5-282">Does the app run in a server farm on multiple servers?</span></span> <span data-ttu-id="492d5-283">如果是，无需其他任何配置，即可在数据保护外使用 Cookie TempData 提供程序（请参阅 <xref:security/data-protection/introduction> 和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)）。</span><span class="sxs-lookup"><span data-stu-id="492d5-283">If so, there's no additional configuration required to use the cookie TempData provider outside of Data Protection (see <xref:security/data-protection/introduction> and [Key storage providers](xref:security/data-protection/implementation/key-storage-providers)).</span></span>

> [!NOTE]
> <span data-ttu-id="492d5-284">大多数 Web 客户端（如 Web 浏览器）针对每个 Cookie 的最大大小和/或 Cookie 总数强制实施限制。</span><span class="sxs-lookup"><span data-stu-id="492d5-284">Most web clients (such as web browsers) enforce limits on the maximum size of each cookie, the total number of cookies, or both.</span></span> <span data-ttu-id="492d5-285">使用 Cookie TempData 提供程序时，请验证应用未超过这些限制。</span><span class="sxs-lookup"><span data-stu-id="492d5-285">When using the cookie TempData provider, verify the app won't exceed these limits.</span></span> <span data-ttu-id="492d5-286">考虑数据的总大小。</span><span class="sxs-lookup"><span data-stu-id="492d5-286">Consider the total size of the data.</span></span> <span data-ttu-id="492d5-287">解释加密和分块导致的 Cookie 大小增加。</span><span class="sxs-lookup"><span data-stu-id="492d5-287">Account for increases in cookie size due to encryption and chunking.</span></span>

### <a name="configure-the-tempdata-provider"></a><span data-ttu-id="492d5-288">配置 TempData 提供程序</span><span class="sxs-lookup"><span data-stu-id="492d5-288">Configure the TempData provider</span></span>

<span data-ttu-id="492d5-289">默认情况下启用基于 Cookie 的 TempData 提供程序。</span><span class="sxs-lookup"><span data-stu-id="492d5-289">The cookie-based TempData provider is enabled by default.</span></span>

<span data-ttu-id="492d5-290">若要启用基于会话的 TempData 提供程序，请使用 [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="492d5-290">To enable the session-based TempData provider, use the [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) extension method:</span></span>

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

<span data-ttu-id="492d5-291">中间件的顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="492d5-291">The order of middleware is important.</span></span> <span data-ttu-id="492d5-292">在前面的示例中，在 `UseMvc` 之后调用 `UseSession` 时会发生 `InvalidOperationException` 异常。</span><span class="sxs-lookup"><span data-stu-id="492d5-292">In the preceding example, an `InvalidOperationException` exception occurs when `UseSession` is invoked after `UseMvc`.</span></span> <span data-ttu-id="492d5-293">有关详细信息，请参阅[中间件排序](xref:fundamentals/middleware/index#order)。</span><span class="sxs-lookup"><span data-stu-id="492d5-293">For more information, see [Middleware Ordering](xref:fundamentals/middleware/index#order).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="492d5-294">如果面向 .NET Framework 并使用基于会话的 TempData 提供程序，请将 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="492d5-294">If targeting .NET Framework and using the session-based TempData provider, add the [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) package to the project.</span></span>

## <a name="query-strings"></a><span data-ttu-id="492d5-295">查询字符串</span><span class="sxs-lookup"><span data-stu-id="492d5-295">Query strings</span></span>

<span data-ttu-id="492d5-296">可以将有限的数据从一个请求传递到另一个请求，方法是将其添加到新请求的查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="492d5-296">A limited amount of data can be passed from one request to another by adding it to the new request's query string.</span></span> <span data-ttu-id="492d5-297">这有利于以一种持久的方式捕获状态，这种方式允许通过电子邮件或社交网络共享嵌入式状态的链接。</span><span class="sxs-lookup"><span data-stu-id="492d5-297">This is useful for capturing state in a persistent manner that allows links with embedded state to be shared through email or social networks.</span></span> <span data-ttu-id="492d5-298">由于 URL 查询字符串是公共的，因此请勿对敏感数据使用查询字符串。</span><span class="sxs-lookup"><span data-stu-id="492d5-298">Because URL query strings are public, never use query strings for sensitive data.</span></span>

<span data-ttu-id="492d5-299">除了意外的共享，在查询字符串中包含数据还会为[跨站点请求伪造 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻击创造机会，从而欺骗用户在通过身份验证时访问恶意网站。</span><span class="sxs-lookup"><span data-stu-id="492d5-299">In addition to unintended sharing, including data in query strings can create opportunities for [Cross-Site Request Forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) attacks, which can trick users into visiting malicious sites while authenticated.</span></span> <span data-ttu-id="492d5-300">然后，攻击者可以从应用中窃取用户数据，或者代表用户采取恶意操作。</span><span class="sxs-lookup"><span data-stu-id="492d5-300">Attackers can then steal user data from the app or take malicious actions on behalf of the user.</span></span> <span data-ttu-id="492d5-301">任何保留的应用或会话状态必须防止 CSRF 攻击。</span><span class="sxs-lookup"><span data-stu-id="492d5-301">Any preserved app or session state must protect against CSRF attacks.</span></span> <span data-ttu-id="492d5-302">有关详细信息，请参阅[预防跨网站请求伪造 (XSRF/CSRF) 攻击](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="492d5-302">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="hidden-fields"></a><span data-ttu-id="492d5-303">隐藏字段</span><span class="sxs-lookup"><span data-stu-id="492d5-303">Hidden fields</span></span>

<span data-ttu-id="492d5-304">数据可以保存在隐藏的表单域中，并在下一个请求上回发。</span><span class="sxs-lookup"><span data-stu-id="492d5-304">Data can be saved in hidden form fields and posted back on the next request.</span></span> <span data-ttu-id="492d5-305">这在多页窗体中很常见。</span><span class="sxs-lookup"><span data-stu-id="492d5-305">This is common in multi-page forms.</span></span> <span data-ttu-id="492d5-306">由于客户端可能篡改数据，因此应用必须始终重新验证存储在隐藏字段中的数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-306">Because the client can potentially tamper with the data, the app must always revalidate the data stored in hidden fields.</span></span>

## <a name="httpcontextitems"></a><span data-ttu-id="492d5-307">HttpContext.Items</span><span class="sxs-lookup"><span data-stu-id="492d5-307">HttpContext.Items</span></span>

<span data-ttu-id="492d5-308">处理单个请求时，使用 [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) 集合存储数据。</span><span class="sxs-lookup"><span data-stu-id="492d5-308">The [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) collection is used to store data while processing a single request.</span></span> <span data-ttu-id="492d5-309">处理请求后，放弃集合的内容。</span><span class="sxs-lookup"><span data-stu-id="492d5-309">The collection's contents are discarded after a request is processed.</span></span> <span data-ttu-id="492d5-310">通常使用 `Items` 集合允许组件或中间件在请求期间在不同时间点操作且没有直接传递参数的方法时进行通信。</span><span class="sxs-lookup"><span data-stu-id="492d5-310">The `Items` collection is often used to allow components or middleware to communicate when they operate at different points in time during a request and have no direct way to pass parameters.</span></span>

<span data-ttu-id="492d5-311">在下面示例中，[中间件](xref:fundamentals/middleware/index)将 `isVerified` 添加到 `Items` 集合。</span><span class="sxs-lookup"><span data-stu-id="492d5-311">In the following example, [middleware](xref:fundamentals/middleware/index) adds `isVerified` to the `Items` collection.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

<span data-ttu-id="492d5-312">然后，在管道中，另一个中间件可以访问 `isVerified` 的值：</span><span class="sxs-lookup"><span data-stu-id="492d5-312">Later in the pipeline, another middleware can access the value of `isVerified`:</span></span>

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

<span data-ttu-id="492d5-313">对于只供单个应用使用的中间件，`string` 键是可以接受的。</span><span class="sxs-lookup"><span data-stu-id="492d5-313">For middleware that's only used by a single app, `string` keys are acceptable.</span></span> <span data-ttu-id="492d5-314">应用实例间共享的中间件应使用唯一的对象键以避免键冲突。</span><span class="sxs-lookup"><span data-stu-id="492d5-314">Middleware shared between app instances should use unique object keys to avoid key collisions.</span></span> <span data-ttu-id="492d5-315">以下示例演示如何使用中间件类中定义的唯一对象键：</span><span class="sxs-lookup"><span data-stu-id="492d5-315">The following example shows how to use a unique object key defined in a middleware class:</span></span>

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

<span data-ttu-id="492d5-316">其他代码可以使用通过中间件类公开的键访问存储在 `HttpContext.Items` 中的值：</span><span class="sxs-lookup"><span data-stu-id="492d5-316">Other code can access the value stored in `HttpContext.Items` using the key exposed by the middleware class:</span></span>

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

<span data-ttu-id="492d5-317">此方法还有避免在代码中使用关键字符串的优势。</span><span class="sxs-lookup"><span data-stu-id="492d5-317">This approach also has the advantage of eliminating the use of key strings in the code.</span></span>

## <a name="cache"></a><span data-ttu-id="492d5-318">缓存</span><span class="sxs-lookup"><span data-stu-id="492d5-318">Cache</span></span>

<span data-ttu-id="492d5-319">缓存是存储和检索数据的有效方法。</span><span class="sxs-lookup"><span data-stu-id="492d5-319">Caching is an efficient way to store and retrieve data.</span></span> <span data-ttu-id="492d5-320">应用可以控制缓存项的生存期。</span><span class="sxs-lookup"><span data-stu-id="492d5-320">The app can control the lifetime of cached items.</span></span>

<span data-ttu-id="492d5-321">缓存数据未与特定请求、用户或会话相关联。</span><span class="sxs-lookup"><span data-stu-id="492d5-321">Cached data isn't associated with a specific request, user, or session.</span></span> <span data-ttu-id="492d5-322">**请注意不要缓存可能由其他用户请求检索的特定于用户的数据。**</span><span class="sxs-lookup"><span data-stu-id="492d5-322">**Be careful not to cache user-specific data that may be retrieved by other users' requests.**</span></span>

<span data-ttu-id="492d5-323">有关详细信息，请参阅 <xref:performance/caching/response>。</span><span class="sxs-lookup"><span data-stu-id="492d5-323">For more information, see <xref:performance/caching/response>.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="492d5-324">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="492d5-324">Dependency Injection</span></span>

<span data-ttu-id="492d5-325">使用[依赖关系注入](xref:fundamentals/dependency-injection)可向所有用户提供数据：</span><span class="sxs-lookup"><span data-stu-id="492d5-325">Use [Dependency Injection](xref:fundamentals/dependency-injection) to make data available to all users:</span></span>

1. <span data-ttu-id="492d5-326">定义一项包含数据的服务。</span><span class="sxs-lookup"><span data-stu-id="492d5-326">Define a service containing the data.</span></span> <span data-ttu-id="492d5-327">例如，定义一个名为 `MyAppData` 的类：</span><span class="sxs-lookup"><span data-stu-id="492d5-327">For example, a class named `MyAppData` is defined:</span></span>

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. <span data-ttu-id="492d5-328">将服务类添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="492d5-328">Add the service class to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. <span data-ttu-id="492d5-329">使用数据服务类：</span><span class="sxs-lookup"><span data-stu-id="492d5-329">Consume the data service class:</span></span>

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a><span data-ttu-id="492d5-330">常见错误</span><span class="sxs-lookup"><span data-stu-id="492d5-330">Common errors</span></span>

* <span data-ttu-id="492d5-331">“在尝试激活‘Microsoft.AspNetCore.Session.DistributedSessionStore’时无法为类型‘Microsoft.Extensions.Caching.Distributed.IDistributedCache’解析服务。”</span><span class="sxs-lookup"><span data-stu-id="492d5-331">"Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'."</span></span>

  <span data-ttu-id="492d5-332">这通常是由于不能配置至少一个 `IDistributedCache` 实现而造成的。</span><span class="sxs-lookup"><span data-stu-id="492d5-332">This is usually caused by failing to configure at least one `IDistributedCache` implementation.</span></span> <span data-ttu-id="492d5-333">有关详细信息，请参阅 <xref:performance/caching/distributed> 和 <xref:performance/caching/memory>。</span><span class="sxs-lookup"><span data-stu-id="492d5-333">For more information, see <xref:performance/caching/distributed> and <xref:performance/caching/memory>.</span></span>

* <span data-ttu-id="492d5-334">在会话中间件保存会话失败的事件中（例如，如果后备存储不可用），中间件记录异常而请求继续正常进行。</span><span class="sxs-lookup"><span data-stu-id="492d5-334">In the event that the session middleware fails to persist a session (for example, if the backing store isn't available), the middleware logs the exception and the request continues normally.</span></span> <span data-ttu-id="492d5-335">这会导致不可预知的行为。</span><span class="sxs-lookup"><span data-stu-id="492d5-335">This leads to unpredictable behavior.</span></span>

  <span data-ttu-id="492d5-336">例如，用户将购物车存储在会话中。</span><span class="sxs-lookup"><span data-stu-id="492d5-336">For example, a user stores a shopping cart in session.</span></span> <span data-ttu-id="492d5-337">用户将商品添加到购物车，但提交失败。</span><span class="sxs-lookup"><span data-stu-id="492d5-337">The user adds an item to the cart but the commit fails.</span></span> <span data-ttu-id="492d5-338">应用不知道有此失败，因此它向用户报告商品已添加到购物车，但事实并非如此。</span><span class="sxs-lookup"><span data-stu-id="492d5-338">The app doesn't know about the failure so it reports to the user that the item was added to their cart, which isn't true.</span></span>

  <span data-ttu-id="492d5-339">检查此类错误的建议方法是完成将应用写入到该会话后，从应用代码调用 `await feature.Session.CommitAsync();`。</span><span class="sxs-lookup"><span data-stu-id="492d5-339">The recommended approach to check for errors is to call `await feature.Session.CommitAsync();` from app code when the app is done writing to the session.</span></span> <span data-ttu-id="492d5-340">如果后备存储不可用，则 `CommitAsync` 引发异常。</span><span class="sxs-lookup"><span data-stu-id="492d5-340">`CommitAsync` throws an exception if the backing store is unavailable.</span></span> <span data-ttu-id="492d5-341">如果 `CommitAsync` 失败，应用可以处理异常。</span><span class="sxs-lookup"><span data-stu-id="492d5-341">If `CommitAsync` fails, the app can process the exception.</span></span> <span data-ttu-id="492d5-342">在与数据存储不可用的相同的条件下，`LoadAsync` 引发异常。</span><span class="sxs-lookup"><span data-stu-id="492d5-342">`LoadAsync` throws under the same conditions where the data store is unavailable.</span></span>
  
## <a name="signalr-and-session-state"></a><span data-ttu-id="492d5-343">SignalR 和会话状态</span><span class="sxs-lookup"><span data-stu-id="492d5-343">SignalR and session state</span></span>

<span data-ttu-id="492d5-344">SignalR 应用不应使用会话状态来存储信息。</span><span class="sxs-lookup"><span data-stu-id="492d5-344">SignalR apps should not use session state to store information.</span></span> <span data-ttu-id="492d5-345">SignalR 应用可以将每个连接状态存储在中心的 `Context.Items` 中。</span><span class="sxs-lookup"><span data-stu-id="492d5-345">SignalR apps can store per connection state in `Context.Items` in the hub.</span></span> <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a><span data-ttu-id="492d5-346">其他资源</span><span class="sxs-lookup"><span data-stu-id="492d5-346">Additional resources</span></span>

<xref:host-and-deploy/web-farm>
