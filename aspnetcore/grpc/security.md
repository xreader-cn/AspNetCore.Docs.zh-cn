---
title: ASP.NET Core 中的 gRPC 的安全注意事项
author: jamesnk
description: 了解 gRPC 的安全注意事项 ASP.NET Core。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: 4a70cb16d8397dbc69a626435fb72a0512788b14
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308750"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core 中的 gRPC 的安全注意事项

按[James 牛顿-k](https://twitter.com/jamesnk)

本文提供了有关通过 .NET Core 保护 gRPC 的信息。

## <a name="transport-security"></a>传输安全

gRPC 消息使用 HTTP/2 来发送和接收。 建议:

* [传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246)用于保护生产 gRPC 应用中的消息。
* gRPC services 应仅侦听并响应受保护的端口。

TLS 是在 Kestrel 中配置的。 有关配置 Kestrel 终结点的详细信息, 请参阅[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。

## <a name="exceptions"></a>Exceptions

异常消息通常被视为不应泄露给客户端的敏感数据。 默认情况下, gRPC 不会将 gRPC 服务引发的异常的详细信息发送到客户端。 相反, 客户端将收到一条指示出错的一般消息。 向客户端发送的异常消息可以通过[EnableDetailedErrors](xref:grpc/configuration#configure-services-options)重写 (例如, 在开发或测试中)。 不应在生产应用程序中向客户端公开异常消息。

## <a name="message-size-limits"></a>消息大小限制

传入消息到 gRPC 的客户端和服务将加载到内存中。 消息大小限制是一种有助于防止 gRPC 消耗过多资源的机制。

gRPC 使用每个消息的大小限制来管理传入消息和传出消息。 默认情况下, gRPC 限制传入消息的大小为 4 MB。 传出消息没有限制。

在服务器上, 可以使用`AddGrpc`以下内容为应用中的所有服务配置 gRPC 消息限制:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 1 * 1024 * 1024;  // 1 megabyte
        options.SendMaxMessageSize = 1 * 1024 * 1024;     // 1 megabyte
    });
}
```

还可以使用`AddServiceOptions<TService>`为单个服务配置限制。 有关配置消息大小限制的详细信息, 请参阅[gRPC 配置](xref:grpc/configuration)。

## <a name="client-certificate-validation"></a>客户端证书验证

建立连接后, 最初会验证[客户端证书](https://tools.ietf.org/html/rfc5246#section-7.4.4)。 默认情况下, Kestrel 不会对连接的客户端证书执行额外的验证。

建议通过客户端证书保护的 gRPC 服务使用[AspNetCore](xref:security/authentication/certauth)包。 ASP.NET Core 认证身份验证将对客户端证书执行其他验证, 包括:

* 证书具有有效的扩展密钥使用 (EKU)
* 在其有效期内
* 检查证书吊销
