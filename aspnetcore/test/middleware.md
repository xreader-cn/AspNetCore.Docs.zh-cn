---
title: 测试 ASP.NET Core 中间件
author: tratcher
description: 了解如何通过 TestServer 测试 ASP.NET Core 中间件。
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876421"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="7ee0c-103">测试 ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="7ee0c-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="7ee0c-104">作者：[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="7ee0c-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="7ee0c-105">中间件可以使用 <xref:Microsoft.AspNetCore.TestHost.TestServer> 单独测试。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="7ee0c-106">这样便可以：</span><span class="sxs-lookup"><span data-stu-id="7ee0c-106">It allows you to:</span></span>

* <span data-ttu-id="7ee0c-107">实例化只包含需要测试的组件的应用管道。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="7ee0c-108">发送自定义请求以验证中间件行为。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="7ee0c-109">优点：</span><span class="sxs-lookup"><span data-stu-id="7ee0c-109">Advantages:</span></span>

* <span data-ttu-id="7ee0c-110">请求会发送到内存中，而不是通过网络进行序列化。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="7ee0c-111">这样可以避免产生额外的问题，例如端口管理和 HTTPS 证书。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="7ee0c-112">中间件中的异常可以直接流回调用测试。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="7ee0c-113">可以直接在测试中自定义服务器数据结构，如 <xref:Microsoft.AspNetCore.Http.HttpContext>。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="7ee0c-114">设置 TestServer</span><span class="sxs-lookup"><span data-stu-id="7ee0c-114">Set up the TestServer</span></span>

<span data-ttu-id="7ee0c-115">在测试项目中，创建测试：</span><span class="sxs-lookup"><span data-stu-id="7ee0c-115">In the test project, create a test:</span></span>

* <span data-ttu-id="7ee0c-116">生成并启动使用 <xref:Microsoft.AspNetCore.TestHost.TestServer> 的主机。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="7ee0c-117">添加中间件使用的任何所需服务。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="7ee0c-118">将处理管道配置为使用中间件进行测试。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="7ee0c-119">使用 HttpClient 发送请求</span><span class="sxs-lookup"><span data-stu-id="7ee0c-119">Send requests with HttpClient</span></span>
<span data-ttu-id="7ee0c-120">使用 <xref:System.Net.Http.HttpClient> 发送请求：</span><span class="sxs-lookup"><span data-stu-id="7ee0c-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="7ee0c-121">断言结果。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-121">Assert the result.</span></span> <span data-ttu-id="7ee0c-122">首先，将断言语句设置为与预期结果相反的结果。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="7ee0c-123">初次运行时，将断言语句设为假正会在中间件正常执行时确认测试未通过。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="7ee0c-124">运行测试并确认测试未通过。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="7ee0c-125">在下面的示例中，在请求根终结点时，中间件应返回 404 状态代码（找不到）  。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="7ee0c-126">使用 `Assert.NotEqual( ... );` 进行首测测试运行，该运行应该会失败：</span><span class="sxs-lookup"><span data-stu-id="7ee0c-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="7ee0c-127">更改断言语句以测试正常操作条件下的中间件。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="7ee0c-128">最终测试使用 `Assert.Equal( ... );`。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="7ee0c-129">再次运行测试，确认该测试已通过。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="7ee0c-130">使用 HttpContext 发送请求</span><span class="sxs-lookup"><span data-stu-id="7ee0c-130">Send requests with HttpContext</span></span>

<span data-ttu-id="7ee0c-131">测试应用还可以使用 [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) 发送请求。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="7ee0c-132">在下面的示例中，当中间件处理 `https://example.com/A/Path/?and=query` 时，会进行几次检查：</span><span class="sxs-lookup"><span data-stu-id="7ee0c-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="7ee0c-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 允许直接配置 <xref:Microsoft.AspNetCore.Http.HttpContext> 对象，而不是使用 <xref:System.Net.Http.HttpClient> 抽象进行配置。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="7ee0c-134">使用 <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 操作仅在服务器上可用的结构，如 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 或 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="7ee0c-135">如测试是否出现“404 - 找不到”响应的前述示例一样，请检查前面测试中每个 `Assert` 语句的相反结果  。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="7ee0c-136">该检查确认中间件正常运行时测试是否正常失败。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="7ee0c-137">确认假正测试正常工作后，为测试的预期条件和值设置最终的 `Assert` 语句。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="7ee0c-138">再次运行测试，确认该测试通过。</span><span class="sxs-lookup"><span data-stu-id="7ee0c-138">Run it again to confirm that the test passes.</span></span>
