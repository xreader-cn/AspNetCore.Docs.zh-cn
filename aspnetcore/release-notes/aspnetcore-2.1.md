---
title: ASP.NET Core 2.1 的新增功能
author: isaac2004
description: 了解 ASP.NET Core 2.1 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: d969b4caab44e3e50b3a0202b25864921d6d01dc
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880861"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="9cdc2-103">ASP.NET Core 2.1 的新增功能</span><span class="sxs-lookup"><span data-stu-id="9cdc2-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="9cdc2-104">本文重点介绍 ASP.NET Core 2.1 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## SignalR

<span data-ttu-id="9cdc2-105">已针对 ASP.NET Core 2.1 重新编写 SignalR。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-105">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="9cdc2-106">ASP.NET Core SignalR 包含大量改进：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-106">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="9cdc2-107">简化横向扩展模型。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-107">A simplified scale-out model.</span></span>
* <span data-ttu-id="9cdc2-108">新 JavaScript 客户端不具有 jQuery 依赖项。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-108">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="9cdc2-109">新紧凑型二进制协议基于 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-109">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="9cdc2-110">支持自定义协议。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-110">Support for custom protocols.</span></span>
* <span data-ttu-id="9cdc2-111">新的流式处理响应模型。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-111">A new streaming response model.</span></span>
* <span data-ttu-id="9cdc2-112">支持基于裸机 WebSocket 的客户端。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-112">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="9cdc2-113">有关详细信息，请参阅 [ASP.NET Core SignalR](xref:signalr/introduction)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-113">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="9cdc2-114">Razor 类库</span><span class="sxs-lookup"><span data-stu-id="9cdc2-114">Razor class libraries</span></span>

<span data-ttu-id="9cdc2-115">通过 ASP.NET Core 2.1 可以更容易地在库中生成和包括基于 Razor 的 UI，并跨多个项目共享 UI。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-115">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="9cdc2-116">新 Razor SDK 支持将 Razor 文件生成到可打包为 NuGet 包的类库项目中。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-116">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="9cdc2-117">应用可以自动发现和覆盖库中的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-117">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="9cdc2-118">通过将 Razor 编译集成到生成中：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-118">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="9cdc2-119">应用启动时间可显著加快。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-119">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="9cdc2-120">在迭代开发工作流过程中，仍可在运行时快速更新 Razor 视图和页面。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-120">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="9cdc2-121">有关详细信息，请参阅[使用 Razor 类库项目创建可重用 UI](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-121">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="9cdc2-122">标识 UI 库和基架</span><span class="sxs-lookup"><span data-stu-id="9cdc2-122">Identity UI library & scaffolding</span></span>

<span data-ttu-id="9cdc2-123">ASP.NET Core 2.1 提供 [ASP.NET Core 标识](xref:security/authentication/identity)作为 [Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-123">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="9cdc2-124">包含标识的应用可以应用新的标识基架，以便有选择地添加标识 Razor 类库 (RCL) 中包含的源代码。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-124">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="9cdc2-125">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-125">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="9cdc2-126">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-126">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="9cdc2-127">生成的代码优先于标识 RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-127">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="9cdc2-128">不包含身份验证的应用可以应用标识基架以添加 RCL 标识包  。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-128">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="9cdc2-129">可以选择要生成的标识代码。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-129">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="9cdc2-130">有关详细信息，请参阅 [ASP.NET Core 项目中的基架标识](xref:security/authentication/scaffold-identity)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-130">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="9cdc2-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="9cdc2-131">HTTPS</span></span>

<span data-ttu-id="9cdc2-132">随着对安全和隐私的关注度日益增加，为 Web 应用启用 HTTPS 变得非常重要。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-132">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="9cdc2-133">Web 上强制使用 HTTPS 的要求日趋严格。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-133">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="9cdc2-134">不使用 HTTPS 的站点被视为不安全的站点。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-134">Sites that don't use HTTPS are considered insecure.</span></span> <span data-ttu-id="9cdc2-135">浏览器（Chromium、Mozilla）开始强制要求必须在安全的上下文中使用 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-135">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="9cdc2-136">[GDPR](xref:security/gdpr) 要求使用 HTTPS 保护用户隐私。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-136">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="9cdc2-137">不仅在生产环境中使用 HTTPS 至关重要，而且在开发环境中使用 HTTPS 还可以帮助防止部署中的各种问题（例如，不安全的链接）。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-137">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="9cdc2-138">ASP.NET Core 2.1 包含大量改进，更方便在开发环境使用 HTTPS 和在生产环境配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-138">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="9cdc2-139">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-139">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="9cdc2-140">默认开启</span><span class="sxs-lookup"><span data-stu-id="9cdc2-140">On by default</span></span>

<span data-ttu-id="9cdc2-141">为了提高网站开发的安全性，现在默认启用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-141">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="9cdc2-142">从 2.1 开始，当本地具有开发证书时，Kestrel 将侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-142">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="9cdc2-143">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-143">A development certificate is created:</span></span>

* <span data-ttu-id="9cdc2-144">首次使用 SDK 时，在首次运行 .NET Core SDK 时会创建开发证书。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-144">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="9cdc2-145">手动使用新 `dev-certs` 工具。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-145">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="9cdc2-146">运行 `dotnet dev-certs https --trust` 以信任证书。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-146">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="9cdc2-147">HTTPS 重定向和强制使用</span><span class="sxs-lookup"><span data-stu-id="9cdc2-147">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="9cdc2-148">Web 应用通常需要侦听 HTTP 和 HTTPS，但随后会将所有 HTTP 流量重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-148">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="9cdc2-149">在 2.1 中，引入了专用的 HTTPS 重定向中间件，可根据配置或绑定服务器端口的存在智能执行重定向。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-149">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="9cdc2-150">使用 [HTTP 严格传输安全协议 (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) 可进一步强制使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-150">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="9cdc2-151">HSTS 指示浏览器始终通过 HTTPS 访问站点。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-151">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="9cdc2-152">ASP.NET Core 2.1 添加 HSTS 中间件，该中间件支持选择最大年限、子域和 HSTS 预加载列表。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-152">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="9cdc2-153">适用于生产环境的配置</span><span class="sxs-lookup"><span data-stu-id="9cdc2-153">Configuration for production</span></span>

<span data-ttu-id="9cdc2-154">在生产环境中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-154">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="9cdc2-155">在 2.1 中，添加了针对 Kestrel 配置 HTTPS 的默认配置架构。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-155">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="9cdc2-156">可以将应用配置为使用：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-156">Apps can be configured to use:</span></span>

* <span data-ttu-id="9cdc2-157">多个终结点（包括 URL）。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-157">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="9cdc2-158">有关详细信息，请参阅 [Kestrel Web 服务器实现：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-158">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="9cdc2-159">来自于磁盘上的文件或证书存储中的用于 HTTPS 的证书。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-159">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="9cdc2-160">GDPR</span><span class="sxs-lookup"><span data-stu-id="9cdc2-160">GDPR</span></span>

<span data-ttu-id="9cdc2-161">ASP.NET Core 提供 API 和模板，帮助满足[欧盟一般数据保护条例 (GDPR)](https://www.eugdpr.org/) 的部分要求。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-161">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="9cdc2-162">有关详细信息，请参阅 [ASP.NET Core 中的 GDPR 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-162">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="9cdc2-163">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)演示如何使用并允许测试已添加到 ASP.NET Core 2.1 模板中的大多数 GDPR 扩展点和 API。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-163">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="9cdc2-164">集成测试</span><span class="sxs-lookup"><span data-stu-id="9cdc2-164">Integration tests</span></span>

<span data-ttu-id="9cdc2-165">引入了可简化创建和执行测试的新包。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-165">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="9cdc2-166">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) 包可处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-166">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="9cdc2-167">将依赖项文件 (\*.deps) 从已测试的应用复制到测试项目的 bin 文件夹中   。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-167">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="9cdc2-168">将内容根目录设置为已测试应用的项目根目录，以便可在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-168">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="9cdc2-169">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化已测试应用在 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 中的启动过程。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-169">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="9cdc2-170">以下测试使用 [xUnit](https://xunit.github.io/) 检查索引页是否加载了成功状态代码和正确的 Content-Type 标头：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-170">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="9cdc2-171">有关详细信息，请参阅[集成测试](xref:test/integration-tests)主题。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-171">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="9cdc2-172">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="9cdc2-172">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="9cdc2-173">ASP.NET Core 2.1 添加了新编程约定，方便构建简洁的说明性 Web API。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-173">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="9cdc2-174">`ActionResult<T>` 是一个新增类型，可允许应用返回响应类型，或返回任何其他操作结果（与 IActionResult 相似），同时仍然指示响应类型。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-174">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="9cdc2-175">此外还添加了 `[ApiController]` 特性，作为选择加入特定于 Web API 的约定和行为的方式。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-175">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="9cdc2-176">有关详细信息，请参阅[使用 ASP.NET Core 构建 Web API](xref:web-api/index)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-176">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="9cdc2-177">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="9cdc2-177">IHttpClientFactory</span></span>

<span data-ttu-id="9cdc2-178">ASP.NET Core 2.1 引入了新的 `IHttpClientFactory` 服务，方便在应用中配置和使用 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-178">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="9cdc2-179">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-179">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="9cdc2-180">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-180">The factory:</span></span>

* <span data-ttu-id="9cdc2-181">使根据已命名客户端注册 `HttpClient` 的实例变得更直观。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-181">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="9cdc2-182">实现一个 Polly 处理程序，允许将 Polly 策略用于 Retry、CircuitBreakers 等。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-182">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="9cdc2-183">有关详细信息，请参阅[启动 HTTP 请求](xref:fundamentals/http-requests)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-183">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="9cdc2-184">Kestrel 传输配置</span><span class="sxs-lookup"><span data-stu-id="9cdc2-184">Kestrel transport configuration</span></span>

<span data-ttu-id="9cdc2-185">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-185">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="9cdc2-186">有关详细信息，请参阅 [Kestrel Web 服务器实现：传输配置](xref:fundamentals/servers/kestrel#transport-configuration)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-186">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="9cdc2-187">通用主机生成器</span><span class="sxs-lookup"><span data-stu-id="9cdc2-187">Generic host builder</span></span>

<span data-ttu-id="9cdc2-188">引入了通用主机生成器 (`HostBuilder`)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-188">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="9cdc2-189">此生成器可用于不处理 HTTP 请求（消息传送、后台任务等等）的应用。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-189">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="9cdc2-190">有关详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-190">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="9cdc2-191">更新的 SPA 模板</span><span class="sxs-lookup"><span data-stu-id="9cdc2-191">Updated SPA templates</span></span>

<span data-ttu-id="9cdc2-192">更新了适用于 Angular、React 和 React 结合 Redux 的单页应用程序模板，以使用标准项目结构和为每个框架构建系统。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-192">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="9cdc2-193">Angular 模板基于 Angular CLI，而 React 模板基于 create-react-app。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-193">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="9cdc2-194">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="9cdc2-194">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="9cdc2-195">Razor Pages 搜索 Razor 资产</span><span class="sxs-lookup"><span data-stu-id="9cdc2-195">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="9cdc2-196">在 2.1 中，Razor Pages 按所列顺序搜索以下目录中的 Razor 资产（例如布局和分区）：</span><span class="sxs-lookup"><span data-stu-id="9cdc2-196">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="9cdc2-197">当前 Pages 文件夹。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-197">Current Pages folder.</span></span>
1. <span data-ttu-id="9cdc2-198">/Pages/Shared/ </span><span class="sxs-lookup"><span data-stu-id="9cdc2-198">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="9cdc2-199">/Views/Shared/ </span><span class="sxs-lookup"><span data-stu-id="9cdc2-199">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="9cdc2-200">某个区域内的 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="9cdc2-200">Razor Pages in an area</span></span>

<span data-ttu-id="9cdc2-201">Razor Pages 现在支持[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-201">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="9cdc2-202">要查看区域示例，请使用个人用户帐户创建新 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-202">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="9cdc2-203">使用个人用户帐户的 Razor Pages Web 应用包括 /Areas/Identity/Pages  。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-203">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="9cdc2-204">MVC 兼容性版本</span><span class="sxs-lookup"><span data-stu-id="9cdc2-204">MVC compatibility version</span></span>

<span data-ttu-id="9cdc2-205"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许应用选择加入或退出 ASP.NET Core MVC 2.1 或更高版本中引入的潜在中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-205">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="9cdc2-206">有关详细信息，请参阅 <xref:mvc/compatibility-version>。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-206">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="9cdc2-207">从 2.0 迁移到 2.1</span><span class="sxs-lookup"><span data-stu-id="9cdc2-207">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="9cdc2-208">请参阅[从 ASP.NET Core 2.0 迁移到 2.1](xref:migration/20_21)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-208">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="9cdc2-209">其他信息</span><span class="sxs-lookup"><span data-stu-id="9cdc2-209">Additional information</span></span>

<span data-ttu-id="9cdc2-210">要获取完整的更改列表，请参阅 [ASP.NET Core 2.1 发行说明](https://github.com/aspnet/Home/releases/tag/2.1.0)。</span><span class="sxs-lookup"><span data-stu-id="9cdc2-210">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
