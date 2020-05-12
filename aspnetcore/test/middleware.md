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
# <a name="test-aspnet-core-middleware"></a>测试 ASP.NET Core 中间件

作者：[Chris Ross](https://github.com/Tratcher)

中间件可以使用 <xref:Microsoft.AspNetCore.TestHost.TestServer> 单独测试。 这样便可以：

* 实例化只包含需要测试的组件的应用管道。
* 发送自定义请求以验证中间件行为。

优点：

* 请求会发送到内存中，而不是通过网络进行序列化。
* 这样可以避免产生额外的问题，例如端口管理和 HTTPS 证书。
* 中间件中的异常可以直接流回调用测试。
* 可以直接在测试中自定义服务器数据结构，如 <xref:Microsoft.AspNetCore.Http.HttpContext>。

## <a name="set-up-the-testserver"></a>设置 TestServer

在测试项目中，创建测试：

* 生成并启动使用 <xref:Microsoft.AspNetCore.TestHost.TestServer> 的主机。
* 添加中间件使用的任何所需服务。
* 将处理管道配置为使用中间件进行测试。

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>使用 HttpClient 发送请求
使用 <xref:System.Net.Http.HttpClient> 发送请求：

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

断言结果。 首先，将断言语句设置为与预期结果相反的结果。 初次运行时，将断言语句设为假正会在中间件正常执行时确认测试未通过。 运行测试并确认测试未通过。

在下面的示例中，在请求根终结点时，中间件应返回 404 状态代码（找不到）  。 使用 `Assert.NotEqual( ... );` 进行首测测试运行，该运行应该会失败：

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

更改断言语句以测试正常操作条件下的中间件。 最终测试使用 `Assert.Equal( ... );`。 再次运行测试，确认该测试已通过。

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>使用 HttpContext 发送请求

测试应用还可以使用 [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) 发送请求。 在下面的示例中，当中间件处理 `https://example.com/A/Path/?and=query` 时，会进行几次检查：

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 允许直接配置 <xref:Microsoft.AspNetCore.Http.HttpContext> 对象，而不是使用 <xref:System.Net.Http.HttpClient> 抽象进行配置。 使用 <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 操作仅在服务器上可用的结构，如 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 或 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)。

如测试是否出现“404 - 找不到”响应的前述示例一样，请检查前面测试中每个 `Assert` 语句的相反结果  。 该检查确认中间件正常运行时测试是否正常失败。 确认假正测试正常工作后，为测试的预期条件和值设置最终的 `Assert` 语句。 再次运行测试，确认该测试通过。
