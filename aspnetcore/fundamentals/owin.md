---
title: ASP.NET Core 中 .NET 的开放 Web 接口 (OWIN)
author: ardalis
description: 了解 ASP.NET Core 如何支持 .NET 的开放 Web 接口 (OWIN)，将 Web 应用与 Web 服务器分离。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78650562"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="24898-103">ASP.NET Core 中 .NET 的开放 Web 接口 (OWIN)</span><span class="sxs-lookup"><span data-stu-id="24898-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="24898-104">作者：[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="24898-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="24898-105">ASP.NET Core 支持 .NET 的开放 Web 接口 (OWIN)。</span><span class="sxs-lookup"><span data-stu-id="24898-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="24898-106">OWIN 允许 Web 应用从 Web 服务器分离。</span><span class="sxs-lookup"><span data-stu-id="24898-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="24898-107">它定义了在管道中使用中间件来处理请求和相关响应的标准方法。</span><span class="sxs-lookup"><span data-stu-id="24898-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="24898-108">ASP.NET Core 应用程序和中间件可以与基于 OWIN 的应用程序、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="24898-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="24898-109">OWIN 提供了一个分离层，可一起使用具有不同对象模型的两个框架。</span><span class="sxs-lookup"><span data-stu-id="24898-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="24898-110">`Microsoft.AspNetCore.Owin` 包提供了两个适配器实现：</span><span class="sxs-lookup"><span data-stu-id="24898-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="24898-111">ASP.NET Core 到 OWIN</span><span class="sxs-lookup"><span data-stu-id="24898-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="24898-112">OWIN 到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24898-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="24898-113">此方法可将 ASP.NET Core 托管在兼容 OWIN 的服务器/主机上，或在 ASP.NET Core 上运行其他兼容 OWIN 的组件。</span><span class="sxs-lookup"><span data-stu-id="24898-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="24898-114">使用这些适配器会带来性能成本。</span><span class="sxs-lookup"><span data-stu-id="24898-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="24898-115">仅使用 ASP.NET Core 组件的应用不应使用 `Microsoft.AspNetCore.Owin` 包或适配器。</span><span class="sxs-lookup"><span data-stu-id="24898-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="24898-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="24898-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="24898-117">在 ASP.NET Core 管道中运行 OWIN 中间件</span><span class="sxs-lookup"><span data-stu-id="24898-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="24898-118">ASP.NET Core 的 OWIN 支持作为 `Microsoft.AspNetCore.Owin` 包的一部分进行部署。</span><span class="sxs-lookup"><span data-stu-id="24898-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="24898-119">可通过安装此包将 OWIN 支持导入到项目中。</span><span class="sxs-lookup"><span data-stu-id="24898-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="24898-120">OWIN 中间件符合 [OWIN 规范](https://owin.org/spec/spec/owin-1.0.0.html)，该规范要求使用 `Func<IDictionary<string, object>, Task>` 接口，并设置特定的键（如 `owin.ResponseBody`）。</span><span class="sxs-lookup"><span data-stu-id="24898-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="24898-121">以下简单的 OWIN 中间件显示“Hello World”：</span><span class="sxs-lookup"><span data-stu-id="24898-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="24898-122">示例签名返回 `Task`，并接受 OWIN 所要求的 `IDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="24898-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="24898-123">以下代码显示了如何使用 `OwinHello` 扩展方法将 `UseOwin` 中间件（如上所示）添加到 ASP.NET Core 管道。</span><span class="sxs-lookup"><span data-stu-id="24898-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="24898-124">可配置在 OWIN 管道中要进行的其他操作。</span><span class="sxs-lookup"><span data-stu-id="24898-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="24898-125">响应标头只能在首次写入响应流之前进行修改。</span><span class="sxs-lookup"><span data-stu-id="24898-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="24898-126">由于性能原因，不建议多次调用 `UseOwin`。</span><span class="sxs-lookup"><span data-stu-id="24898-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="24898-127">组合在一起时 OWIN 组件的性能最佳。</span><span class="sxs-lookup"><span data-stu-id="24898-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="24898-128">在基于 OWIN 的服务器中使用 ASP.NET Core 托管</span><span class="sxs-lookup"><span data-stu-id="24898-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="24898-129">基于 OWIN 的服务器可托管 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="24898-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="24898-130">[Nowin](https://github.com/Bobris/Nowin)（.NET OWIN Web 服务器）属于这种服务器。</span><span class="sxs-lookup"><span data-stu-id="24898-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="24898-131">本文的示例中包含了一个引用 Nowin 的项目，并使用它创建了一个自托管 ASP.NET Core 的 `IServer`。</span><span class="sxs-lookup"><span data-stu-id="24898-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="24898-132">`IServer` 是需要 `Features` 属性和 `Start` 方法的接口。</span><span class="sxs-lookup"><span data-stu-id="24898-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="24898-133">`Start` 负责配置和启动服务器，在此情况下，此操作通过一系列 Fluent API 调用完成，这些调用设置从 IServerAddressesFeature 分析的地址。</span><span class="sxs-lookup"><span data-stu-id="24898-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="24898-134">请注意，`_builder` 变量的 Fluent 配置指定请求将由方法中之前定义的 `appFunc` 来处理。</span><span class="sxs-lookup"><span data-stu-id="24898-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="24898-135">对于每个请求，都会调用此 `Func` 以处理传入请求。</span><span class="sxs-lookup"><span data-stu-id="24898-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="24898-136">我们还将添加一个 `IWebHostBuilder` 扩展，以便添加和配置 Nowin 服务器。</span><span class="sxs-lookup"><span data-stu-id="24898-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="24898-137">完成此操作后，调用 Program.cs 中的扩展以使用此自定义服务器运行 ASP.NET Core 应用  ：</span><span class="sxs-lookup"><span data-stu-id="24898-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="24898-138">了解有关 [ASP.NET Core 服务器](xref:fundamentals/servers/index)的更多信息。</span><span class="sxs-lookup"><span data-stu-id="24898-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="24898-139">在基于 OWIN 的服务器上运行 ASP.NET Core 并使用其 WebSocket 支持</span><span class="sxs-lookup"><span data-stu-id="24898-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="24898-140">ASP.NET Core 如何利用基于 OWIN 的服务器功能的另一个示例是访问 WebSocket 等功能。</span><span class="sxs-lookup"><span data-stu-id="24898-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="24898-141">前面示例中使用的 .NET OWIN Web 服务器支持内置的 Web 套接字，可由 ASP.NET Core 应用程序利用。</span><span class="sxs-lookup"><span data-stu-id="24898-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="24898-142">下面的示例显示了简单的 Web 应用，它支持 Web 套接字并回显通过 WebSocket 发送到服务器的所有内容。</span><span class="sxs-lookup"><span data-stu-id="24898-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="24898-143">使用与前一个相同的 [ 来配置此](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)示例`NowinServer` - 唯一的区别是如何在其 `Configure` 方法中配置应用程序。</span><span class="sxs-lookup"><span data-stu-id="24898-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="24898-144">使用[简单的 websocket 客户端](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en)的测试演示应用程序：</span><span class="sxs-lookup"><span data-stu-id="24898-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Web 套接字测试客户端](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="24898-146">OWIN 环境</span><span class="sxs-lookup"><span data-stu-id="24898-146">OWIN environment</span></span>

<span data-ttu-id="24898-147">可使用 `HttpContext` 来构造 OWIN 环境。</span><span class="sxs-lookup"><span data-stu-id="24898-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="24898-148">OWIN 键</span><span class="sxs-lookup"><span data-stu-id="24898-148">OWIN keys</span></span>

<span data-ttu-id="24898-149">OWIN 依赖于 `IDictionary<string,object>` 对象，以在整个 HTTP请求/响应交换中传达信息。</span><span class="sxs-lookup"><span data-stu-id="24898-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="24898-150">ASP.NET Core 实现以下所列的键。</span><span class="sxs-lookup"><span data-stu-id="24898-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="24898-151">请参阅[主规范、扩展](https://owin.org/#spec)和 [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html)（OWIN 键指南和常用键）。</span><span class="sxs-lookup"><span data-stu-id="24898-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="24898-152">请求数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="24898-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="24898-153">项</span><span class="sxs-lookup"><span data-stu-id="24898-153">Key</span></span>               | <span data-ttu-id="24898-154">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-154">Value (type)</span></span> | <span data-ttu-id="24898-155">描述</span><span class="sxs-lookup"><span data-stu-id="24898-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="24898-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="24898-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="24898-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="24898-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="24898-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="24898-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="24898-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="24898-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="24898-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="24898-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="24898-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="24898-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="24898-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="24898-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="24898-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="24898-164">请求数据 (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="24898-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="24898-165">项</span><span class="sxs-lookup"><span data-stu-id="24898-165">Key</span></span>               | <span data-ttu-id="24898-166">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-166">Value (type)</span></span> | <span data-ttu-id="24898-167">描述</span><span class="sxs-lookup"><span data-stu-id="24898-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="24898-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="24898-169">Optional</span><span class="sxs-lookup"><span data-stu-id="24898-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="24898-170">响应数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="24898-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="24898-171">项</span><span class="sxs-lookup"><span data-stu-id="24898-171">Key</span></span>               | <span data-ttu-id="24898-172">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-172">Value (type)</span></span> | <span data-ttu-id="24898-173">描述</span><span class="sxs-lookup"><span data-stu-id="24898-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="24898-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="24898-175">Optional</span><span class="sxs-lookup"><span data-stu-id="24898-175">Optional</span></span> |
| <span data-ttu-id="24898-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="24898-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="24898-177">Optional</span><span class="sxs-lookup"><span data-stu-id="24898-177">Optional</span></span> |
| <span data-ttu-id="24898-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="24898-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="24898-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="24898-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="24898-180">其他数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="24898-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="24898-181">项</span><span class="sxs-lookup"><span data-stu-id="24898-181">Key</span></span>               | <span data-ttu-id="24898-182">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-182">Value (type)</span></span> | <span data-ttu-id="24898-183">描述</span><span class="sxs-lookup"><span data-stu-id="24898-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="24898-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="24898-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="24898-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="24898-186">常用键</span><span class="sxs-lookup"><span data-stu-id="24898-186">Common keys</span></span>

| <span data-ttu-id="24898-187">项</span><span class="sxs-lookup"><span data-stu-id="24898-187">Key</span></span>               | <span data-ttu-id="24898-188">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-188">Value (type)</span></span> | <span data-ttu-id="24898-189">描述</span><span class="sxs-lookup"><span data-stu-id="24898-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="24898-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="24898-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="24898-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="24898-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="24898-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="24898-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="24898-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="24898-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="24898-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="24898-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="24898-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="24898-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="24898-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="24898-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="24898-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="24898-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="24898-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="24898-199">项</span><span class="sxs-lookup"><span data-stu-id="24898-199">Key</span></span>               | <span data-ttu-id="24898-200">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-200">Value (type)</span></span> | <span data-ttu-id="24898-201">描述</span><span class="sxs-lookup"><span data-stu-id="24898-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="24898-202">sendfile.SendAsync</span></span> | <span data-ttu-id="24898-203">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="24898-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="24898-204">每请求</span><span class="sxs-lookup"><span data-stu-id="24898-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="24898-205">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="24898-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="24898-206">项</span><span class="sxs-lookup"><span data-stu-id="24898-206">Key</span></span>               | <span data-ttu-id="24898-207">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-207">Value (type)</span></span> | <span data-ttu-id="24898-208">描述</span><span class="sxs-lookup"><span data-stu-id="24898-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="24898-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="24898-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="24898-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="24898-211">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="24898-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="24898-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="24898-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="24898-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="24898-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="24898-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="24898-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="24898-215">项</span><span class="sxs-lookup"><span data-stu-id="24898-215">Key</span></span>               | <span data-ttu-id="24898-216">值（类型）</span><span class="sxs-lookup"><span data-stu-id="24898-216">Value (type)</span></span> | <span data-ttu-id="24898-217">描述</span><span class="sxs-lookup"><span data-stu-id="24898-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="24898-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="24898-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="24898-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="24898-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="24898-220">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="24898-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="24898-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="24898-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="24898-222">非规范</span><span class="sxs-lookup"><span data-stu-id="24898-222">Non-spec</span></span> |
| <span data-ttu-id="24898-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="24898-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="24898-224">请参阅 [RFC6455 4.2.2 节](https://tools.ietf.org/html/rfc6455#section-4.2.2)步骤 5.5</span><span class="sxs-lookup"><span data-stu-id="24898-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="24898-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="24898-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="24898-226">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="24898-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="24898-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="24898-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="24898-228">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="24898-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="24898-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="24898-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="24898-230">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="24898-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="24898-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="24898-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="24898-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="24898-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="24898-233">Optional</span><span class="sxs-lookup"><span data-stu-id="24898-233">Optional</span></span> |
| <span data-ttu-id="24898-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="24898-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="24898-235">Optional</span><span class="sxs-lookup"><span data-stu-id="24898-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="24898-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="24898-236">Additional resources</span></span>

* [<span data-ttu-id="24898-237">中间件</span><span class="sxs-lookup"><span data-stu-id="24898-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="24898-238">服务器</span><span class="sxs-lookup"><span data-stu-id="24898-238">Servers</span></span>](xref:fundamentals/servers/index)
