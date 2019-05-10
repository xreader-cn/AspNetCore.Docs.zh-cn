---
title: ASP.NET Core 2.1 的新增功能
author: isaac2004
description: 了解 ASP.NET Core 2.1 的新增功能。
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2019
uid: aspnetcore-2.1
ms.openlocfilehash: 359f961db768b9048427c8ab296ee3e035879408
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086396"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="da8e8-103">ASP.NET Core 2.1 的新增功能</span><span class="sxs-lookup"><span data-stu-id="da8e8-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="da8e8-104">本文重点介绍 ASP.NET Core 2.1 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="da8e8-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## <a name="signalr"></a><span data-ttu-id="da8e8-105">SignalR</span><span class="sxs-lookup"><span data-stu-id="da8e8-105">SignalR</span></span>

<span data-ttu-id="da8e8-106">已针对 ASP.NET Core 2.1 重新编写 SignalR。</span><span class="sxs-lookup"><span data-stu-id="da8e8-106">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="da8e8-107">ASP.NET Core SignalR 包含大量改进：</span><span class="sxs-lookup"><span data-stu-id="da8e8-107">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="da8e8-108">简化横向扩展模型。</span><span class="sxs-lookup"><span data-stu-id="da8e8-108">A simplified scale-out model.</span></span>
* <span data-ttu-id="da8e8-109">新 JavaScript 客户端不具有 jQuery 依赖项。</span><span class="sxs-lookup"><span data-stu-id="da8e8-109">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="da8e8-110">新紧凑型二进制协议基于 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="da8e8-110">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="da8e8-111">支持自定义协议。</span><span class="sxs-lookup"><span data-stu-id="da8e8-111">Support for custom protocols.</span></span>
* <span data-ttu-id="da8e8-112">新的流式处理响应模型。</span><span class="sxs-lookup"><span data-stu-id="da8e8-112">A new streaming response model.</span></span>
* <span data-ttu-id="da8e8-113">支持基于裸机 WebSocket 的客户端。</span><span class="sxs-lookup"><span data-stu-id="da8e8-113">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="da8e8-114">有关详细信息，请参阅 [ASP.NET Core SignalR](xref:signalr/introduction)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-114">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="da8e8-115">Razor 类库</span><span class="sxs-lookup"><span data-stu-id="da8e8-115">Razor class libraries</span></span>

<span data-ttu-id="da8e8-116">通过 ASP.NET Core 2.1 可以更容易地在库中生成和包括基于 Razor 的 UI，并跨多个项目共享 UI。</span><span class="sxs-lookup"><span data-stu-id="da8e8-116">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="da8e8-117">新 Razor SDK 支持将 Razor 文件生成到可打包为 NuGet 包的类库项目中。</span><span class="sxs-lookup"><span data-stu-id="da8e8-117">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="da8e8-118">应用可以自动发现和覆盖库中的视图和页面。</span><span class="sxs-lookup"><span data-stu-id="da8e8-118">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="da8e8-119">通过将 Razor 编译集成到生成中：</span><span class="sxs-lookup"><span data-stu-id="da8e8-119">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="da8e8-120">应用启动时间可显著加快。</span><span class="sxs-lookup"><span data-stu-id="da8e8-120">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="da8e8-121">在迭代开发工作流过程中，仍可在运行时快速更新 Razor 视图和页面。</span><span class="sxs-lookup"><span data-stu-id="da8e8-121">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="da8e8-122">有关详细信息，请参阅[使用 Razor 类库项目创建可重用 UI](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-122">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="da8e8-123">标识 UI 库和基架</span><span class="sxs-lookup"><span data-stu-id="da8e8-123">Identity UI library & scaffolding</span></span>

<span data-ttu-id="da8e8-124">ASP.NET Core 2.1 提供 [ASP.NET Core 标识](xref:security/authentication/identity)作为 [Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-124">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="da8e8-125">包含标识的应用可以应用新的标识基架，以便有选择地添加标识 Razor 类库 (RCL) 中包含的源代码。</span><span class="sxs-lookup"><span data-stu-id="da8e8-125">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="da8e8-126">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="da8e8-126">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="da8e8-127">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="da8e8-127">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="da8e8-128">生成的代码优先于标识 RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="da8e8-128">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="da8e8-129">不包含身份验证的应用可以应用标识基架以添加 RCL 标识包。</span><span class="sxs-lookup"><span data-stu-id="da8e8-129">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="da8e8-130">可以选择要生成的标识代码。</span><span class="sxs-lookup"><span data-stu-id="da8e8-130">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="da8e8-131">有关详细信息，请参阅 [ASP.NET Core 项目中的基架标识](xref:security/authentication/scaffold-identity)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-131">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="da8e8-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="da8e8-132">HTTPS</span></span>

<span data-ttu-id="da8e8-133">随着对安全和隐私的关注度日益增加，为 Web 应用启用 HTTPS 变得非常重要。</span><span class="sxs-lookup"><span data-stu-id="da8e8-133">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="da8e8-134">Web 上正在越来越严格要求强制使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="da8e8-134">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="da8e8-135">不使用 HTTPS 的站点会被视为不安全的站点。</span><span class="sxs-lookup"><span data-stu-id="da8e8-135">Sites that don’t use HTTPS are considered insecure.</span></span> <span data-ttu-id="da8e8-136">浏览器（Chromium、Mozilla）开始强制要求必须在安全的上下文中使用 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="da8e8-136">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="da8e8-137">[GDPR](xref:security/gdpr) 要求使用 HTTPS 保护用户隐私。</span><span class="sxs-lookup"><span data-stu-id="da8e8-137">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="da8e8-138">不仅在生产环境中使用 HTTPS 至关重要，而且在开发环境中使用 HTTPS 还可以帮助防止部署中的各种问题（例如，不安全的链接）。</span><span class="sxs-lookup"><span data-stu-id="da8e8-138">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="da8e8-139">ASP.NET Core 2.1 包含大量改进，更方便在开发环境使用 HTTPS 和在生产环境配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="da8e8-139">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="da8e8-140">有关详细信息，请参阅[强制使用 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-140">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="da8e8-141">默认开启</span><span class="sxs-lookup"><span data-stu-id="da8e8-141">On by default</span></span>

<span data-ttu-id="da8e8-142">为了提高网站开发的安全性，现在默认启用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="da8e8-142">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="da8e8-143">从 2.1 开始，当本地具有开发证书时，Kestrel 将侦听 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="da8e8-143">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="da8e8-144">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="da8e8-144">A development certificate is created:</span></span>

* <span data-ttu-id="da8e8-145">首次使用 SDK 时，在首次运行 .NET Core SDK 时会创建开发证书。</span><span class="sxs-lookup"><span data-stu-id="da8e8-145">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="da8e8-146">手动使用新 `dev-certs` 工具。</span><span class="sxs-lookup"><span data-stu-id="da8e8-146">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="da8e8-147">运行 `dotnet dev-certs https --trust` 以信任证书。</span><span class="sxs-lookup"><span data-stu-id="da8e8-147">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="da8e8-148">HTTPS 重定向和强制使用</span><span class="sxs-lookup"><span data-stu-id="da8e8-148">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="da8e8-149">Web 应用通常需要侦听 HTTP 和 HTTPS，但随后会将所有 HTTP 流量重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="da8e8-149">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="da8e8-150">在 2.1 中，引入了专用的 HTTPS 重定向中间件，可根据配置或绑定服务器端口的存在智能执行重定向。</span><span class="sxs-lookup"><span data-stu-id="da8e8-150">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="da8e8-151">使用 [HTTP 严格传输安全协议 (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) 可进一步强制使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="da8e8-151">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="da8e8-152">HSTS 指示浏览器始终通过 HTTPS 访问站点。</span><span class="sxs-lookup"><span data-stu-id="da8e8-152">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="da8e8-153">ASP.NET Core 2.1 添加 HSTS 中间件，该中间件支持选择最大年限、子域和 HSTS 预加载列表。</span><span class="sxs-lookup"><span data-stu-id="da8e8-153">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="da8e8-154">适用于生产环境的配置</span><span class="sxs-lookup"><span data-stu-id="da8e8-154">Configuration for production</span></span>

<span data-ttu-id="da8e8-155">在生产环境中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="da8e8-155">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="da8e8-156">在 2.1 中，添加了针对 Kestrel 配置 HTTPS 的默认配置架构。</span><span class="sxs-lookup"><span data-stu-id="da8e8-156">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="da8e8-157">可以将应用配置为使用：</span><span class="sxs-lookup"><span data-stu-id="da8e8-157">Apps can be configured to use:</span></span>

* <span data-ttu-id="da8e8-158">多个终结点（包括 URL）。</span><span class="sxs-lookup"><span data-stu-id="da8e8-158">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="da8e8-159">有关详细信息，请参阅 [Kestrel Web 服务器实现：终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-159">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="da8e8-160">来自于磁盘上的文件或证书存储中的用于 HTTPS 的证书。</span><span class="sxs-lookup"><span data-stu-id="da8e8-160">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="da8e8-161">GDPR</span><span class="sxs-lookup"><span data-stu-id="da8e8-161">GDPR</span></span>

<span data-ttu-id="da8e8-162">ASP.NET Core 提供 API 和模板，帮助满足[欧盟一般数据保护条例 (GDPR)](https://www.eugdpr.org/) 的部分要求。</span><span class="sxs-lookup"><span data-stu-id="da8e8-162">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="da8e8-163">有关详细信息，请参阅 [ASP.NET Core 中的 GDPR 支持](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-163">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="da8e8-164">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)演示如何使用并允许测试已添加到 ASP.NET Core 2.1 模板中的大多数 GDPR 扩展点和 API。</span><span class="sxs-lookup"><span data-stu-id="da8e8-164">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="da8e8-165">集成测试</span><span class="sxs-lookup"><span data-stu-id="da8e8-165">Integration tests</span></span>

<span data-ttu-id="da8e8-166">引入了可简化创建和执行测试的新包。</span><span class="sxs-lookup"><span data-stu-id="da8e8-166">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="da8e8-167">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) 包可处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="da8e8-167">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="da8e8-168">将依赖项文件 (\*.deps) 从已测试的应用复制到测试项目的 bin 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="da8e8-168">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="da8e8-169">将内容根目录设置为已测试应用的项目根目录，以便可在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="da8e8-169">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="da8e8-170">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化已测试应用在 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 中的启动过程。</span><span class="sxs-lookup"><span data-stu-id="da8e8-170">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="da8e8-171">以下测试使用 [xUnit](https://xunit.github.io/) 检查索引页是否加载了成功状态代码和正确的 Content-Type 标头：</span><span class="sxs-lookup"><span data-stu-id="da8e8-171">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

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

<span data-ttu-id="da8e8-172">有关详细信息，请参阅[集成测试](xref:test/integration-tests)主题。</span><span class="sxs-lookup"><span data-stu-id="da8e8-172">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="da8e8-173">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="da8e8-173">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="da8e8-174">ASP.NET Core 2.1 添加了新编程约定，方便构建简洁的说明性 Web API。</span><span class="sxs-lookup"><span data-stu-id="da8e8-174">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="da8e8-175">`ActionResult<T>` 是一个新增类型，可允许应用返回响应类型，或返回任何其他操作结果（与 IActionResult 相似），同时仍然指示响应类型。</span><span class="sxs-lookup"><span data-stu-id="da8e8-175">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="da8e8-176">此外还添加了 `[ApiController]` 特性，作为选择加入特定于 Web API 的约定和行为的方式。</span><span class="sxs-lookup"><span data-stu-id="da8e8-176">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="da8e8-177">有关详细信息，请参阅[使用 ASP.NET Core 构建 Web API](xref:web-api/index)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-177">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="da8e8-178">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="da8e8-178">IHttpClientFactory</span></span>

<span data-ttu-id="da8e8-179">ASP.NET Core 2.1 引入了新的 `IHttpClientFactory` 服务，方便在应用中配置和使用 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="da8e8-179">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="da8e8-180">`HttpClient` 已经具有委托处理程序的概念，这些委托处理程序可以链接在一起，处理出站 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="da8e8-180">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="da8e8-181">工厂可以：</span><span class="sxs-lookup"><span data-stu-id="da8e8-181">The factory:</span></span>

* <span data-ttu-id="da8e8-182">使根据已命名客户端注册 `HttpClient` 的实例变得更直观。</span><span class="sxs-lookup"><span data-stu-id="da8e8-182">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="da8e8-183">实现一个 Polly 处理程序，允许将 Polly 策略用于 Retry、CircuitBreakers 等。</span><span class="sxs-lookup"><span data-stu-id="da8e8-183">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="da8e8-184">有关详细信息，请参阅[启动 HTTP 请求](xref:fundamentals/http-requests)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-184">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="da8e8-185">Kestrel 传输配置</span><span class="sxs-lookup"><span data-stu-id="da8e8-185">Kestrel transport configuration</span></span>

<span data-ttu-id="da8e8-186">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="da8e8-186">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="da8e8-187">有关详细信息，请参阅 [Kestrel Web 服务器实现：传输配置](xref:fundamentals/servers/kestrel#transport-configuration)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-187">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="da8e8-188">通用主机生成器</span><span class="sxs-lookup"><span data-stu-id="da8e8-188">Generic host builder</span></span>

<span data-ttu-id="da8e8-189">引入了通用主机生成器 (`HostBuilder`)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-189">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="da8e8-190">此生成器可用于不处理 HTTP 请求（消息传送、后台任务等等）的应用。</span><span class="sxs-lookup"><span data-stu-id="da8e8-190">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="da8e8-191">有关详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-191">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="da8e8-192">更新的 SPA 模板</span><span class="sxs-lookup"><span data-stu-id="da8e8-192">Updated SPA templates</span></span>

<span data-ttu-id="da8e8-193">更新了适用于 Angular、React 和 React 结合 Redux 的单页应用程序模板，以使用标准项目结构和为每个框架构建系统。</span><span class="sxs-lookup"><span data-stu-id="da8e8-193">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="da8e8-194">Angular 模板基于 Angular CLI，而 React 模板基于 create-react-app。</span><span class="sxs-lookup"><span data-stu-id="da8e8-194">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="da8e8-195">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="da8e8-195">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="da8e8-196">Razor Pages 搜索 Razor 资产</span><span class="sxs-lookup"><span data-stu-id="da8e8-196">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="da8e8-197">在 2.1 中，Razor Pages 按所列顺序搜索以下目录中的 Razor 资产（例如布局和分区）：</span><span class="sxs-lookup"><span data-stu-id="da8e8-197">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="da8e8-198">当前 Pages 文件夹。</span><span class="sxs-lookup"><span data-stu-id="da8e8-198">Current Pages folder.</span></span>
1. <span data-ttu-id="da8e8-199">/Pages/Shared/</span><span class="sxs-lookup"><span data-stu-id="da8e8-199">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="da8e8-200">/Views/Shared/</span><span class="sxs-lookup"><span data-stu-id="da8e8-200">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="da8e8-201">某个区域内的 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="da8e8-201">Razor Pages in an area</span></span>

<span data-ttu-id="da8e8-202">Razor Pages 现在支持[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-202">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="da8e8-203">要查看区域示例，请使用个人用户帐户创建新 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="da8e8-203">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="da8e8-204">使用个人用户帐户的 Razor Pages Web 应用包括 /Areas/Identity/Pages。</span><span class="sxs-lookup"><span data-stu-id="da8e8-204">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="da8e8-205">MVC 兼容性版本</span><span class="sxs-lookup"><span data-stu-id="da8e8-205">MVC compatibility version</span></span>

<span data-ttu-id="da8e8-206"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许应用选择加入或退出 ASP.NET Core MVC 2.1 或更高版本中引入的潜在中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="da8e8-206">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="da8e8-207">有关更多信息，请参见<xref:mvc/compatibility-version>。</span><span class="sxs-lookup"><span data-stu-id="da8e8-207">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="da8e8-208">从 2.0 迁移到 2.1</span><span class="sxs-lookup"><span data-stu-id="da8e8-208">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="da8e8-209">请参阅[从 ASP.NET Core 2.0 迁移到 2.1](xref:migration/20_21)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-209">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="da8e8-210">其他信息</span><span class="sxs-lookup"><span data-stu-id="da8e8-210">Additional information</span></span>

<span data-ttu-id="da8e8-211">要获取完整的更改列表，请参阅 [ASP.NET Core 2.1 发行说明](https://github.com/aspnet/Home/releases/tag/2.1.0)。</span><span class="sxs-lookup"><span data-stu-id="da8e8-211">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
