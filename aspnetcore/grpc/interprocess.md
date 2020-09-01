---
title: 使用 gRPC 进行进程间通信
author: jamesnk
description: 了解如何使用 gRPC 进行进程间通信。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
no-loc:
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945455"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="763b8-103">使用 gRPC 进行进程间通信</span><span class="sxs-lookup"><span data-stu-id="763b8-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="763b8-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="763b8-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="763b8-105">客户端和服务之间的 gRPC 调用通常通过 TCP 套接字发送。</span><span class="sxs-lookup"><span data-stu-id="763b8-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="763b8-106">TCP 非常适用于网络中的通信，但当客户端和服务在同一台计算机上时，[进程间通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) 的效率更高。</span><span class="sxs-lookup"><span data-stu-id="763b8-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="763b8-107">本文档介绍如何在 IPC 场景中将 gRPC 用于自定义传输。</span><span class="sxs-lookup"><span data-stu-id="763b8-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="763b8-108">服务器配置</span><span class="sxs-lookup"><span data-stu-id="763b8-108">Server configuration</span></span>

<span data-ttu-id="763b8-109">Kestrel 支持自定义传输。</span><span class="sxs-lookup"><span data-stu-id="763b8-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="763b8-110">在 Program.cs 上配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="763b8-110">Kestrel is configured in *Program.cs*:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

<span data-ttu-id="763b8-111">上面的示例：</span><span class="sxs-lookup"><span data-stu-id="763b8-111">The preceding example:</span></span>

* <span data-ttu-id="763b8-112">在 `ConfigureKestrel` 中配置 Kestrel 的终结点。</span><span class="sxs-lookup"><span data-stu-id="763b8-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="763b8-113">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 来侦听具有指定路径的 [Unix 域套接字 (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket)。</span><span class="sxs-lookup"><span data-stu-id="763b8-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="763b8-114">Kestrel 提供对 UDS 终结点的内置支持。</span><span class="sxs-lookup"><span data-stu-id="763b8-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="763b8-115">UDS 在 Linux、macOS 和 [Windows 的新式版本](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/)上受支持。</span><span class="sxs-lookup"><span data-stu-id="763b8-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="763b8-116">客户端配置</span><span class="sxs-lookup"><span data-stu-id="763b8-116">Client configuration</span></span>

<span data-ttu-id="763b8-117">`GrpcChannel` 支持通过自定义传输进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="763b8-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="763b8-118">创建通道后，可以使用包含自定义 `ConnectionFactory` 的 `SocketsHttpHandler` 来配置它。</span><span class="sxs-lookup"><span data-stu-id="763b8-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="763b8-119">工厂允许客户端通过自定义传输建立连接，然后通过该传输发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="763b8-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="763b8-120">`ConnectionFactory` 是 .NET 5 候选发布 1 中的新 API。</span><span class="sxs-lookup"><span data-stu-id="763b8-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="763b8-121">Unix 域套接字连接工厂示例：</span><span class="sxs-lookup"><span data-stu-id="763b8-121">Unix domain sockets connection factory example:</span></span>

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

<span data-ttu-id="763b8-122">使用自定义连接工厂创建通道：</span><span class="sxs-lookup"><span data-stu-id="763b8-122">Using the custom connection factory to create a channel:</span></span>

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

<span data-ttu-id="763b8-123">使用上述代码创建的通道通过 Unix 域套接字发送 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="763b8-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
