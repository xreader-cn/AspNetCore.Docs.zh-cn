---
title: ASP.NET Core 2.2 的新增功能
author: tdykstra
description: 了解 ASP.NET Core 2.2 的新增功能。
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: aspnetcore-2.2
ms.openlocfilehash: cdc761b645b91777bdf6084c3ad4659fcea55039
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750939"
---
# <a name="whats-new-in-aspnet-core-22"></a><span data-ttu-id="3b8db-103">ASP.NET Core 2.2 的新增功能</span><span class="sxs-lookup"><span data-stu-id="3b8db-103">What's new in ASP.NET Core 2.2</span></span>

<span data-ttu-id="3b8db-104">本文重点介绍 ASP.NET Core 2.2 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="3b8db-104">This article highlights the most significant changes in ASP.NET Core 2.2, with links to relevant documentation.</span></span>

## <a name="openapi-analyzers--conventions"></a><span data-ttu-id="3b8db-105">开放 API 分析器和约定</span><span class="sxs-lookup"><span data-stu-id="3b8db-105">OpenAPI Analyzers & Conventions</span></span>

<span data-ttu-id="3b8db-106">开放 API（以前称为 Swagger）是一个与语言无关的规范，用于描述 REST API。</span><span class="sxs-lookup"><span data-stu-id="3b8db-106">OpenAPI (formerly known as Swagger) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="3b8db-107">开放 API 生态系统具有一些工具，可用于发现、测试和生成使用该规范的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="3b8db-107">The OpenAPI ecosystem has tools that allow for discovering, testing, and producing client code using the specification.</span></span> <span data-ttu-id="3b8db-108">对在 ASP.NET Core MVC 中生成和可视化开放 API 文档的支持通过社区驱动型项目（如 [NSwag](https://github.com/RSuter/NSwag) 和 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)）来提供。</span><span class="sxs-lookup"><span data-stu-id="3b8db-108">Support for generating and visualizing OpenAPI documents in ASP.NET Core MVC is provided via community driven projects such as [NSwag](https://github.com/RSuter/NSwag), and [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="3b8db-109">对于创建开放 API 文档，ASP.NET Core 2.2 提供了改进的工具和运行时体验。</span><span class="sxs-lookup"><span data-stu-id="3b8db-109">ASP.NET Core 2.2 provides improved tooling and runtime experiences for creating OpenAPI documents.</span></span>

<span data-ttu-id="3b8db-110">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="3b8db-110">For more information, see the following resources:</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [<span data-ttu-id="3b8db-111">ASP.NET Core 2.2.0-preview1：开放 API 分析器和约定</span><span class="sxs-lookup"><span data-stu-id="3b8db-111">ASP.NET Core 2.2.0-preview1: OpenAPI Analyzers & Conventions</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a><span data-ttu-id="3b8db-112">问题详细信息支持</span><span class="sxs-lookup"><span data-stu-id="3b8db-112">Problem details support</span></span>

<span data-ttu-id="3b8db-113">ASP.NET Core 2.1 引入了 `ProblemDetails`（基于用于通过 HTTP 响应传送错误详细信息的 [RFC 7807](https://tools.ietf.org/html/rfc7807) 规范）。</span><span class="sxs-lookup"><span data-stu-id="3b8db-113">ASP.NET Core 2.1 introduced `ProblemDetails`, based on the [RFC 7807](https://tools.ietf.org/html/rfc7807) specification for carrying details of an error with an HTTP Response.</span></span> <span data-ttu-id="3b8db-114">在 2.2 中，`ProblemDetails` 是针对具有 `ApiControllerAttribute` 特性的控制器中客户端错误代码的标准响应。</span><span class="sxs-lookup"><span data-stu-id="3b8db-114">In 2.2, `ProblemDetails` is the standard response for client error codes in controllers attributed with `ApiControllerAttribute`.</span></span> <span data-ttu-id="3b8db-115">返回客户端错误状态代码 (4xx) 的 `IActionResult` 现在返回 `ProblemDetails` 正文。</span><span class="sxs-lookup"><span data-stu-id="3b8db-115">An `IActionResult` returning a client error status code (4xx) now returns a `ProblemDetails` body.</span></span> <span data-ttu-id="3b8db-116">结果还包括可以用于将使用请求日志的错误相关联的关联 ID。</span><span class="sxs-lookup"><span data-stu-id="3b8db-116">The result also includes a correlation ID that can be used to correlate the error using request logs.</span></span> <span data-ttu-id="3b8db-117">对于客户端错误，`ProducesResponseType` 默认为使用 `ProblemDetails` 作为响应类型。</span><span class="sxs-lookup"><span data-stu-id="3b8db-117">For client errors, `ProducesResponseType` defaults to using `ProblemDetails` as the response type.</span></span> <span data-ttu-id="3b8db-118">这会记录在使用 NSwag 或 Swashbuckle.AspNetCore 生成的开放 API/Swagger 输出中。</span><span class="sxs-lookup"><span data-stu-id="3b8db-118">This is documented in OpenAPI / Swagger output generated using NSwag or Swashbuckle.AspNetCore.</span></span>

## <a name="endpoint-routing"></a><span data-ttu-id="3b8db-119">终结点路由</span><span class="sxs-lookup"><span data-stu-id="3b8db-119">Endpoint Routing</span></span>

<span data-ttu-id="3b8db-120">ASP.NET Core 2.2 使用新的终结点路由系统来改进请求的调度。</span><span class="sxs-lookup"><span data-stu-id="3b8db-120">ASP.NET Core 2.2 uses a new *endpoint routing* system for improved dispatching of requests.</span></span> <span data-ttu-id="3b8db-121">更改包括新链接生成 API 成员和路由参数转换器。</span><span class="sxs-lookup"><span data-stu-id="3b8db-121">The changes include new link generation API members and route parameter transformers.</span></span>

<span data-ttu-id="3b8db-122">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="3b8db-122">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3b8db-123">2.2 中的终结点路由</span><span class="sxs-lookup"><span data-stu-id="3b8db-123">Endpoint routing in 2.2</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* <span data-ttu-id="3b8db-124">[路由参数转换器](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx)（请参阅“路由”部分）</span><span class="sxs-lookup"><span data-stu-id="3b8db-124">[Route parameter transformers](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (see **Routing** section)</span></span>
* [<span data-ttu-id="3b8db-125">基于 IRouter 与基于终结点的路由之间的差异</span><span class="sxs-lookup"><span data-stu-id="3b8db-125">Differences between IRouter- and endpoint-based routing</span></span>](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a><span data-ttu-id="3b8db-126">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="3b8db-126">Health checks</span></span>

<span data-ttu-id="3b8db-127">通过新的运行状况检查服务可以更轻松地在需要运行状况检查的环境（如 Kubernetes）中使用 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="3b8db-127">A new health checks service makes it easier to use ASP.NET Core in environments that require health checks, such as Kubernetes.</span></span> <span data-ttu-id="3b8db-128">运行状况检查包括中间件和一组定义 `IHealthCheck` 抽象和服务的库。</span><span class="sxs-lookup"><span data-stu-id="3b8db-128">Health checks includes middleware and a set of libraries that define an `IHealthCheck` abstraction and service.</span></span>

<span data-ttu-id="3b8db-129">运行状况检查由容器业务流程协调程序或负载均衡器用于快速确定系统是否正常响应请求。</span><span class="sxs-lookup"><span data-stu-id="3b8db-129">Health checks are used by a container orchestrator or load balancer to quickly determine if a system is responding to requests normally.</span></span> <span data-ttu-id="3b8db-130">容器业务流程协调程序可以通过停止滚动部署或重新启动容器来响应失败的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="3b8db-130">A container orchestrator might respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="3b8db-131">负载均衡器可以通过路由流量以避开失败的服务实例，来响应运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="3b8db-131">A load balancer might respond to a health check by routing traffic away from the failing instance of the service.</span></span>

<span data-ttu-id="3b8db-132">运行状况检查由应用程序公开为监视系统所使用的 HTTP 终结点。</span><span class="sxs-lookup"><span data-stu-id="3b8db-132">Health checks are exposed by an application as an HTTP endpoint used by monitoring systems.</span></span> <span data-ttu-id="3b8db-133">可以为各种实时监视方案和监视系统配置运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="3b8db-133">Health checks can be configured for a variety of real-time monitoring scenarios and monitoring systems.</span></span> <span data-ttu-id="3b8db-134">运行状况检查服务与 [BeatPulse 项目](https://github.com/Xabaril/BeatPulse)集成。</span><span class="sxs-lookup"><span data-stu-id="3b8db-134">The health checks service integrates with the [BeatPulse project](https://github.com/Xabaril/BeatPulse).</span></span> <span data-ttu-id="3b8db-135">从而可以更轻松地添加为众多常用系统和依赖项添加检查。</span><span class="sxs-lookup"><span data-stu-id="3b8db-135">which makes it easier to add checks for dozens of popular systems and dependencies.</span></span>

<span data-ttu-id="3b8db-136">有关详细信息，请参阅 [ASP.NET Core 中的运行状况检查](xref:host-and-deploy/health-checks)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-136">For more information, see [Health checks in ASP.NET Core](xref:host-and-deploy/health-checks).</span></span>

## <a name="http2-in-kestrel"></a><span data-ttu-id="3b8db-137">Kestrel 中的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="3b8db-137">HTTP/2 in Kestrel</span></span>

<span data-ttu-id="3b8db-138">ASP.NET Core 2.2 添加了对 HTTP/2 的支持。</span><span class="sxs-lookup"><span data-stu-id="3b8db-138">ASP.NET Core 2.2 adds support for HTTP/2.</span></span> 

<span data-ttu-id="3b8db-139">HTTP/2 是 HTTP 协议的主要修订版本。</span><span class="sxs-lookup"><span data-stu-id="3b8db-139">HTTP/2 is a major revision of the HTTP protocol.</span></span> <span data-ttu-id="3b8db-140">HTTP/2 的一些值得注意的功能包括支持标头压缩，以及单个连接上的完整多路复用流。</span><span class="sxs-lookup"><span data-stu-id="3b8db-140">Some of the notable features of HTTP/2 are support for header compression and fully multiplexed streams over a single connection.</span></span> <span data-ttu-id="3b8db-141">尽管 HTTP/2 保留了 HTTP 的语义（HTTP 标头、方法等），不过与 HTTP/1.x 相比，在线路上对此数据进行组帧和发送的方式发生了重大更改。</span><span class="sxs-lookup"><span data-stu-id="3b8db-141">While HTTP/2 preserves HTTP’s semantics (HTTP headers, methods, etc) it's a breaking change from HTTP/1.x on how this data is framed and sent over the wire.</span></span>

<span data-ttu-id="3b8db-142">由于组帧中的这一更改，服务器和客户端需要协商所使用的协议版本。</span><span class="sxs-lookup"><span data-stu-id="3b8db-142">As a consequence of this change in framing, servers and clients need to negotiate the protocol version used.</span></span> <span data-ttu-id="3b8db-143">应用层协议协商 (ALPN) 是一种 TLS 扩展，允许服务器和客户端协商用作其 TLS 握手一部分的协议版本。</span><span class="sxs-lookup"><span data-stu-id="3b8db-143">Application-Layer Protocol Negotiation (ALPN) is a TLS extension that allows the server and client to negotiate the protocol version used as part of their TLS handshake.</span></span> <span data-ttu-id="3b8db-144">虽然服务器与客户端之间可以事先了解协议，不过所有主要浏览器都支持 ALPN 作为建立 HTTP/2 连接的唯一方法。</span><span class="sxs-lookup"><span data-stu-id="3b8db-144">While it is possible to have prior knowledge between the server and the client on the protocol, all major browsers support ALPN as the only way to establish an HTTP/2 connection.</span></span>

<span data-ttu-id="3b8db-145">有关详细信息，请参阅 [HTTP/2 支持](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-145">For more information, see [HTTP/2 support](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support).</span></span>

## <a name="kestrel-configuration"></a><span data-ttu-id="3b8db-146">Kestrel 配置</span><span class="sxs-lookup"><span data-stu-id="3b8db-146">Kestrel configuration</span></span>

<span data-ttu-id="3b8db-147">在早期版本的 ASP.NET Core 中，Kestrel 选项通过调用 `UseKestrel` 来配置。</span><span class="sxs-lookup"><span data-stu-id="3b8db-147">In earlier versions of ASP.NET Core, Kestrel options are configured by calling `UseKestrel`.</span></span> <span data-ttu-id="3b8db-148">在 2.2 中，Kestrel 选项通过在主机生成器上调用 `ConfigureKestrel` 来配置。</span><span class="sxs-lookup"><span data-stu-id="3b8db-148">In 2.2, Kestrel options are configured by calling `ConfigureKestrel` on the host builder.</span></span> <span data-ttu-id="3b8db-149">此更改为进程内承载解决了 `IServer` 注册顺序方面的问题。</span><span class="sxs-lookup"><span data-stu-id="3b8db-149">This change resolves an issue with the order of `IServer` registrations for in-process hosting.</span></span> <span data-ttu-id="3b8db-150">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="3b8db-150">For more information, see the following resources:</span></span>

* [<span data-ttu-id="3b8db-151">缓解 UseIIS 冲突</span><span class="sxs-lookup"><span data-stu-id="3b8db-151">Mitigate UseIIS conflict</span></span>](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [<span data-ttu-id="3b8db-152">使用 ConfigureKestrel 配置 Kestrel 服务器选项</span><span class="sxs-lookup"><span data-stu-id="3b8db-152">Configure Kestrel server options with ConfigureKestrel</span></span>](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a><span data-ttu-id="3b8db-153">IIS 进程内承载</span><span class="sxs-lookup"><span data-stu-id="3b8db-153">IIS in-process hosting</span></span>

<span data-ttu-id="3b8db-154">在早期版本的 ASP.NET Core 中，IIS 用作反向代理。</span><span class="sxs-lookup"><span data-stu-id="3b8db-154">In earlier versions of ASP.NET Core, IIS serves as a reverse proxy.</span></span> <span data-ttu-id="3b8db-155">在 2.2 中，ASP.NET Core 模块可以启动 CoreCLR 并在 IIS 工作进程 (w3wp.exe) 内承载应用。</span><span class="sxs-lookup"><span data-stu-id="3b8db-155">In 2.2, the ASP.NET Core Module can boot the CoreCLR and host an app inside the IIS worker process (*w3wp.exe*).</span></span> <span data-ttu-id="3b8db-156">在使用 IIS 运行时，进程内承载可提供性能和诊断提升。</span><span class="sxs-lookup"><span data-stu-id="3b8db-156">In-process hosting provides performance and diagnostic gains when running with IIS.</span></span>

<span data-ttu-id="3b8db-157">有关详细信息，请参阅 [IIS 进程内承载](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-157">For more information, see [in-process hosting for IIS](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model).</span></span>

## <a name="signalr-java-client"></a><span data-ttu-id="3b8db-158">SignalR Java 客户端</span><span class="sxs-lookup"><span data-stu-id="3b8db-158">SignalR Java client</span></span>

<span data-ttu-id="3b8db-159">ASP.NET Core 2.2 引入了适用于 SignalR 的 Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="3b8db-159">ASP.NET Core 2.2 introduces a Java Client for SignalR.</span></span> <span data-ttu-id="3b8db-160">此客户端支持通过 Java 代码连接到 ASP.NET Core SignalR 服务器（包括 Android 应用）。</span><span class="sxs-lookup"><span data-stu-id="3b8db-160">This client supports connecting to an ASP.NET Core SignalR Server from Java code, including Android apps.</span></span>

<span data-ttu-id="3b8db-161">有关详细信息，请参阅 [ASP.NET Core SignalR Java 客户端](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-161">For more information, see [ASP.NET Core SignalR Java client](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2).</span></span>

## <a name="cors-improvements"></a><span data-ttu-id="3b8db-162">CORS 改进</span><span class="sxs-lookup"><span data-stu-id="3b8db-162">CORS improvements</span></span>

<span data-ttu-id="3b8db-163">在早期版本的 ASP.NET Core 中，CORS 中间件允许发送 `Accept`、`Accept-Language`、`Content-Language` 和 `Origin` 标头（不考虑在 `CorsPolicy.Headers` 中配置的值）。</span><span class="sxs-lookup"><span data-stu-id="3b8db-163">In earlier versions of ASP.NET Core, CORS Middleware allows `Accept`, `Accept-Language`, `Content-Language`, and `Origin` headers to be sent regardless of the values configured in `CorsPolicy.Headers`.</span></span> <span data-ttu-id="3b8db-164">在 2.2 中，仅当在 `Access-Control-Request-Headers` 中发送的标头与 `WithHeaders` 中声明的标头完全匹配时，才能进行 CORS 中间件策略匹配。</span><span class="sxs-lookup"><span data-stu-id="3b8db-164">In 2.2, a CORS Middleware policy match is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="3b8db-165">有关详细信息，请参阅 [CORS 中间件](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-165">For more information, see [CORS Middleware](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers).</span></span>

## <a name="response-compression"></a><span data-ttu-id="3b8db-166">响应压缩</span><span class="sxs-lookup"><span data-stu-id="3b8db-166">Response compression</span></span>

<span data-ttu-id="3b8db-167">ASP.NET Core 2.2 可以使用 [Brotli 压缩格式](https://tools.ietf.org/html/rfc7932)来压缩响应。</span><span class="sxs-lookup"><span data-stu-id="3b8db-167">ASP.NET Core 2.2 can compress responses with the [Brotli compression format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="3b8db-168">有关详细信息，请参阅[响应压缩中间件支持 Brotli 压缩](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-168">For more information, see [Response Compression Middleware supports Brotli compression](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider).</span></span>

## <a name="project-templates"></a><span data-ttu-id="3b8db-169">项目模板</span><span class="sxs-lookup"><span data-stu-id="3b8db-169">Project templates</span></span>

<span data-ttu-id="3b8db-170">ASP.NET Core Web 项目模板已更新为 [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) 和 [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-170">ASP.NET Core web project templates were updated to [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) and [Angular 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4).</span></span> <span data-ttu-id="3b8db-171">新的外观在视觉上更简单，可以更轻松地查看应用的重要结构。</span><span class="sxs-lookup"><span data-stu-id="3b8db-171">The new look is visually simpler and makes it easier to see the important structures of the app.</span></span>

![主页或索引页](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a><span data-ttu-id="3b8db-173">验证性能</span><span class="sxs-lookup"><span data-stu-id="3b8db-173">Validation performance</span></span>

<span data-ttu-id="3b8db-174">MVC 的验证系统设计为具有可扩展性和灵活性，从而使你可以基于每个请求确定应用于给定模型的验证程序。</span><span class="sxs-lookup"><span data-stu-id="3b8db-174">MVC’s validation system is designed to be extensible and flexible, allowing you to determine on a per request basis which validators apply to a given model.</span></span> <span data-ttu-id="3b8db-175">这非常适用于创作复杂的验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="3b8db-175">This is great for authoring complex validation providers.</span></span> <span data-ttu-id="3b8db-176">但是在最常见的情况下，应用程序仅使用内置验证程序，无需这一额外的灵活性。</span><span class="sxs-lookup"><span data-stu-id="3b8db-176">However, in the most common case an application only uses the built-in validators and don’t require this extra flexibility.</span></span> <span data-ttu-id="3b8db-177">内置验证程序包括 DataAnnotations（如 [Required] 和 [StringLength]）和 `IValidatableObject`。</span><span class="sxs-lookup"><span data-stu-id="3b8db-177">Built-in validators include DataAnnotations such as [Required] and [StringLength], and `IValidatableObject`.</span></span>

<span data-ttu-id="3b8db-178">在 ASP.NET Core 2.2 中，如果 MVC 确定给定模型关系图不需要验证，则可以绕过验证。</span><span class="sxs-lookup"><span data-stu-id="3b8db-178">In ASP.NET Core 2.2, MVC can short-circuit validation if it determines that a given model graph doesn't require validation.</span></span> <span data-ttu-id="3b8db-179">在验证无法具有或不具有任何验证程序的模型时，跳过验证可获得显著改进。</span><span class="sxs-lookup"><span data-stu-id="3b8db-179">Skipping validation results in significant improvements when validating models that can't or don't have any validators.</span></span> <span data-ttu-id="3b8db-180">这包括诸如基元集合（如 `byte[]`、`string[]`、`Dictionary<string, string>` ）之类的对象，或没有许多验证程序的复杂对象关系图。</span><span class="sxs-lookup"><span data-stu-id="3b8db-180">This includes objects such as collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`), or complex object graphs without many validators.</span></span>

## <a name="http-client-performance"></a><span data-ttu-id="3b8db-181">HTTP 客户端性能</span><span class="sxs-lookup"><span data-stu-id="3b8db-181">HTTP Client performance</span></span>

<span data-ttu-id="3b8db-182">在 ASP.NET Core 2.2 中，通过减少连接池锁定争用提高了 `SocketsHttpHandler` 的性能。</span><span class="sxs-lookup"><span data-stu-id="3b8db-182">In ASP.NET Core 2.2, the performance of `SocketsHttpHandler` was improved by reducing connection pool locking contention.</span></span> <span data-ttu-id="3b8db-183">对于进行大量传出 HTTP 请求的应用（如某些微服务体系结构），吞吐量得到了提高。</span><span class="sxs-lookup"><span data-stu-id="3b8db-183">For apps that make many outgoing HTTP requests, such as some microservices architectures, throughput is improved.</span></span> <span data-ttu-id="3b8db-184">在负载下，`HttpClient` 吞吐量在 Linux 上可以提高多达 60%，在 Windows 上提高多达 20%。</span><span class="sxs-lookup"><span data-stu-id="3b8db-184">Under load, `HttpClient` throughput can be improved by up to 60% on Linux and 20% on Windows.</span></span>

<span data-ttu-id="3b8db-185">有关详细信息，请参阅[实现此改进的拉取请求](https://github.com/dotnet/corefx/pull/32568)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-185">For more information, see [the pull request that made this improvement](https://github.com/dotnet/corefx/pull/32568).</span></span>

## <a name="additional-information"></a><span data-ttu-id="3b8db-186">其他信息</span><span class="sxs-lookup"><span data-stu-id="3b8db-186">Additional information</span></span>

<span data-ttu-id="3b8db-187">要获取完整的更改列表，请参阅 [ASP.NET Core 2.2 发行说明](https://github.com/aspnet/Home/releases/tag/2.2.0)。</span><span class="sxs-lookup"><span data-stu-id="3b8db-187">For the complete list of changes, see the [ASP.NET Core 2.2 Release Notes](https://github.com/aspnet/Home/releases/tag/2.2.0).</span></span>
