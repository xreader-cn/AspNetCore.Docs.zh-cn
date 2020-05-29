---
title: 测试 ASP.NET Core 中间件
author: tratcher
description: 了解如何通过 TestServer 测试 ASP.NET Core 中间件。
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: ea7fc0e889ab32cbaf23257b3e866519af0727aa
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424541"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="651b4-103">测试 ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="651b4-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="651b4-104">作者：[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="651b4-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="651b4-105">中间件可以使用 <xref:Microsoft.AspNetCore.TestHost.TestServer> 单独测试。</span><span class="sxs-lookup"><span data-stu-id="651b4-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="651b4-106">这样便可以：</span><span class="sxs-lookup"><span data-stu-id="651b4-106">It allows you to:</span></span>

* <span data-ttu-id="651b4-107">实例化只包含需要测试的组件的应用管道。</span><span class="sxs-lookup"><span data-stu-id="651b4-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="651b4-108">发送自定义请求以验证中间件行为。</span><span class="sxs-lookup"><span data-stu-id="651b4-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="651b4-109">优点：</span><span class="sxs-lookup"><span data-stu-id="651b4-109">Advantages:</span></span>

* <span data-ttu-id="651b4-110">请求会发送到内存中，而不是通过网络进行序列化。</span><span class="sxs-lookup"><span data-stu-id="651b4-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="651b4-111">这样可以避免产生额外的问题，例如端口管理和 HTTPS 证书。</span><span class="sxs-lookup"><span data-stu-id="651b4-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="651b4-112">中间件中的异常可以直接流回调用测试。</span><span class="sxs-lookup"><span data-stu-id="651b4-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="651b4-113">可以直接在测试中自定义服务器数据结构，如 <xref:Microsoft.AspNetCore.Http.HttpContext>。</span><span class="sxs-lookup"><span data-stu-id="651b4-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="651b4-114">设置 TestServer</span><span class="sxs-lookup"><span data-stu-id="651b4-114">Set up the TestServer</span></span>

<span data-ttu-id="651b4-115">在测试项目中，创建测试：</span><span class="sxs-lookup"><span data-stu-id="651b4-115">In the test project, create a test:</span></span>

* <span data-ttu-id="651b4-116">生成并启动使用 <xref:Microsoft.AspNetCore.TestHost.TestServer> 的主机。</span><span class="sxs-lookup"><span data-stu-id="651b4-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="651b4-117">添加中间件使用的任何所需服务。</span><span class="sxs-lookup"><span data-stu-id="651b4-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="651b4-118">将处理管道配置为使用中间件进行测试。</span><span class="sxs-lookup"><span data-stu-id="651b4-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="651b4-119">使用 HttpClient 发送请求</span><span class="sxs-lookup"><span data-stu-id="651b4-119">Send requests with HttpClient</span></span>
<span data-ttu-id="651b4-120">使用 <xref:System.Net.Http.HttpClient> 发送请求：</span><span class="sxs-lookup"><span data-stu-id="651b4-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="651b4-121">断言结果。</span><span class="sxs-lookup"><span data-stu-id="651b4-121">Assert the result.</span></span> <span data-ttu-id="651b4-122">首先，将断言语句设置为与预期结果相反的结果。</span><span class="sxs-lookup"><span data-stu-id="651b4-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="651b4-123">初次运行时，将断言语句设为假正会在中间件正常执行时确认测试未通过。</span><span class="sxs-lookup"><span data-stu-id="651b4-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="651b4-124">运行测试并确认测试未通过。</span><span class="sxs-lookup"><span data-stu-id="651b4-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="651b4-125">在下面的示例中，在请求根终结点时，中间件应返回 404 状态代码（找不到）。</span><span class="sxs-lookup"><span data-stu-id="651b4-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="651b4-126">使用 `Assert.NotEqual( ... );` 进行首测测试运行，该运行应该会失败：</span><span class="sxs-lookup"><span data-stu-id="651b4-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="651b4-127">更改断言语句以测试正常操作条件下的中间件。</span><span class="sxs-lookup"><span data-stu-id="651b4-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="651b4-128">最终测试使用 `Assert.Equal( ... );`。</span><span class="sxs-lookup"><span data-stu-id="651b4-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="651b4-129">再次运行测试，确认该测试已通过。</span><span class="sxs-lookup"><span data-stu-id="651b4-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="651b4-130">使用 HttpContext 发送请求</span><span class="sxs-lookup"><span data-stu-id="651b4-130">Send requests with HttpContext</span></span>

<span data-ttu-id="651b4-131">测试应用还可以使用 [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) 发送请求。</span><span class="sxs-lookup"><span data-stu-id="651b4-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="651b4-132">在下面的示例中，当中间件处理 `https://example.com/A/Path/?and=query` 时，会进行几次检查：</span><span class="sxs-lookup"><span data-stu-id="651b4-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="651b4-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 允许直接配置 <xref:Microsoft.AspNetCore.Http.HttpContext> 对象，而不是使用 <xref:System.Net.Http.HttpClient> 抽象进行配置。</span><span class="sxs-lookup"><span data-stu-id="651b4-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="651b4-134">使用 <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 操作仅在服务器上可用的结构，如 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 或 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)。</span><span class="sxs-lookup"><span data-stu-id="651b4-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="651b4-135">如测试是否出现“404 - 找不到”响应的前述示例一样，请检查前面测试中每个 `Assert` 语句的相反结果。</span><span class="sxs-lookup"><span data-stu-id="651b4-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="651b4-136">该检查确认中间件正常运行时测试是否正常失败。</span><span class="sxs-lookup"><span data-stu-id="651b4-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="651b4-137">确认假正测试正常工作后，为测试的预期条件和值设置最终的 `Assert` 语句。</span><span class="sxs-lookup"><span data-stu-id="651b4-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="651b4-138">再次运行测试，确认该测试通过。</span><span class="sxs-lookup"><span data-stu-id="651b4-138">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="651b4-139">TestServer 限制</span><span class="sxs-lookup"><span data-stu-id="651b4-139">TestServer limitations</span></span>

<span data-ttu-id="651b4-140">TestServer：</span><span class="sxs-lookup"><span data-stu-id="651b4-140">TestServer:</span></span>

* <span data-ttu-id="651b4-141">用于复制服务器行为以测试中间件。</span><span class="sxs-lookup"><span data-stu-id="651b4-141">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="651b4-142">请勿尝试复制所有 <xref:System.Net.Http.HttpClient> 行为。</span><span class="sxs-lookup"><span data-stu-id="651b4-142">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="651b4-143">尝试授予客户端对服务器尽可能多控制权的访问权限，并尽可能深入地了解服务器上发生的情况。</span><span class="sxs-lookup"><span data-stu-id="651b4-143">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="651b4-144">例如，它可能引发 `HttpClient` 通常不会引发的异常，以便直接传输服务器状态。</span><span class="sxs-lookup"><span data-stu-id="651b4-144">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="651b4-145">默认情况下，不会设置某些传输特定标头，因为这些标头通常与中间件无关。</span><span class="sxs-lookup"><span data-stu-id="651b4-145">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="651b4-146">有关更多信息，请参见下一节。</span><span class="sxs-lookup"><span data-stu-id="651b4-146">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="651b4-147">Content-Length 和 Transfer-Encoding 标头</span><span class="sxs-lookup"><span data-stu-id="651b4-147">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="651b4-148">TestServer 不设置与传输相关的请求或响应标头，如 [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) 或 [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding)。</span><span class="sxs-lookup"><span data-stu-id="651b4-148">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="651b4-149">应用程序应避免依赖于这些标头，因为它们的用法因客户端、方案和协议而异。</span><span class="sxs-lookup"><span data-stu-id="651b4-149">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="651b4-150">如果需要 `Content-Length` 和 `Transfer-Encoding` 来测试特定方案，则可以在编写 <xref:System.Net.Http.HttpRequestMessage> 或 <xref:Microsoft.AspNetCore.Http.HttpContext> 时在测试中指定它们。</span><span class="sxs-lookup"><span data-stu-id="651b4-150">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="651b4-151">有关详细信息，请查看以下 GitHub 问题：</span><span class="sxs-lookup"><span data-stu-id="651b4-151">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="651b4-152">dotnet/aspnetcore#21677</span><span class="sxs-lookup"><span data-stu-id="651b4-152">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="651b4-153">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="651b4-153">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="651b4-154">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="651b4-154">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
