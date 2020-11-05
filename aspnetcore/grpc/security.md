---
title: 适用于 ASP.NET Core 的 gRPC 的安全注意事项
author: jamesnk
description: 了解适用于 ASP.NET Core 的 gRPC 的安全注意事项。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: a7a595a71f988377bf25c500f04da2add3d85aef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058826"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>适用于 ASP.NET Core 的 gRPC 的安全注意事项

作者：[James Newton-King](https://twitter.com/jamesnk)

本文提供了有关通过 .NET Core 保护 gRPC 的信息。

## <a name="transport-security"></a>传输安全性

gRPC 消息使用 HTTP/2 进行发送和接收。 我们建议：

* 使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246) 以保护生产 gRPC 应用中的消息。
* gRPC 服务应仅侦听并响应受保护的端口。

TLS 是在 Kestrel 中配置的。 有关配置 Kestrel 终结点的详细信息，请参阅 [Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。

## <a name="exceptions"></a>异常

异常消息通常被认为是不应透露给客户端的敏感数据。 默认情况下，gRPC 不会将 gRPC 服务引发的异常的详细信息发送给客户端。 客户端将收到指示出现错误的一般消息。 使用 [EnableDetailedErrors](xref:grpc/configuration#configure-services-options) 可以替代（例如，在开发或测试中）向客户端的异常消息发送。 异常消息不应在生产应用中向客户端公开。

## <a name="message-size-limits"></a>消息大小限制

到 gRPC 客户端和服务的传入消息将加载到内存中。 消息大小限制是一种有助于防止 gRPC 消耗过多资源的机制。

gRPC 使用每个消息的大小限制来管理传入和传出消息。 默认情况下，gRPC 将传入消息限制为 4 MB。 传出消息没有限制。

在服务器上，可以使用 `AddGrpc` 为应用中的所有服务配置 gRPC 消息限制：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

还可以使用 `AddServiceOptions<TService>` 为单个服务配置限制。 有关配置消息大小限制的详细信息，请参阅 [gRPC 配置](xref:grpc/configuration)。

## <a name="client-certificate-validation"></a>客户端证书验证

建立连接后，将首先验证[客户端证书](https://tools.ietf.org/html/rfc5246#section-7.4.4)。 默认情况下，Kestrel 不会对连接的客户端证书执行额外验证。

建议由客户端证书保护的 gRPC 服务使用 [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) 包。 ASP.NET Core 认证身份验证将对客户端证书执行其他验证，包括：

* 验证证书是否具有有效的增强型密钥使用 (EKU)
* 验证是否在其有效期内
* 检查证书吊销
