---
title: 测试 ASP.NET Core 中间件
author: tratcher
description: 了解如何通过 TestServer 测试 ASP.NET Core 中间件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- appsettings.json
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93051429"
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
* 将 [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet 包添加到项目：
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* 将处理管道配置为使用中间件进行测试。

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>使用 HttpClient 发送请求

使用 <xref:System.Net.Http.HttpClient> 发送请求：

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

断言结果。 首先，将断言语句设置为与预期结果相反的结果。 初次运行时，将断言语句设为假正会在中间件正常执行时确认测试未通过。 运行测试并确认测试未通过。

在下面的示例中，在请求根终结点时，中间件应返回 404 状态代码（找不到）。 使用 `Assert.NotEqual( ... );` 进行首测测试运行，该运行应该会失败：

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

如测试是否出现“404 - 找不到”响应的前述示例一样，请检查前面测试中每个 `Assert` 语句的相反结果。 该检查确认中间件正常运行时测试是否正常失败。 确认假正测试正常工作后，为测试的预期条件和值设置最终的 `Assert` 语句。 再次运行测试，确认该测试通过。

## <a name="testserver-limitations"></a>TestServer 限制

TestServer：

* 用于复制服务器行为以测试中间件。
* 请勿*_尝试复制所有 <xref:System.Net.Http.HttpClient> 行为。
_ 尝试授予客户端对服务器尽可能多控制权的访问权限，并尽可能深入地了解服务器上发生的情况。 例如，它可能引发 `HttpClient` 通常不会引发的异常，以便直接传输服务器状态。
* 默认情况下，不会设置某些传输特定标头，因为这些标头通常与中间件无关。 有关更多信息，请参见下一节。

### <a name="content-length-and-transfer-encoding-headers"></a>Content-Length 和 Transfer-Encoding 标头

TestServer 不*_设置与传输相关的请求或响应标头，如 [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) 或 [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding)。 应用程序应避免依赖于这些标头，因为它们的用法因客户端、方案和协议而异。 如果需要 `Content-Length` 和 `Transfer-Encoding` 来测试特定方案，则可以在编写 <xref:System.Net.Http.HttpRequestMessage> 或 <xref:Microsoft.AspNetCore.Http.HttpContext> 时在测试中指定它们。 有关详细信息，请查看以下 GitHub 问题：

_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore#18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore#13273](https://github.com/dotnet/aspnetcore/issues/13273)
