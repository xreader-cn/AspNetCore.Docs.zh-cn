---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="grpc-for-net-configuration"></a>适用于 .NET 的 gRPC 配置

## <a name="configure-services-options"></a>配置服务选项

gRPC 服务在 Startup.cs 中使用 `AddGrpc` 进行配置。 下表描述了用于配置 gRPC 服务的选项：

| 选项 | 默认值 | 描述 |
| ---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------ | | MaxSendMessageSize | `null` | 可以从服务器发送的最大消息大小（以字节为单位）。 尝试发送超过配置的最大消息大小的消息会导致异常。 设置为 `null`时，消息的大小不受限制。 | | MaxReceiveMessageSize | 4 MB | 可以由服务器接收的最大消息大小（以字节为单位）。 如果服务器收到的消息超过此限制，则会引发异常。 增大此值可使服务器接收更大的消息，但可能会对内存消耗产生负面影响。 设置为 `null`时，消息的大小不受限制。 | | EnableDetailedErrors | `false` | 如果为 `true`，则当服务方法中引发异常时，会将详细异常消息返回到客户端。 默认值为 `false`。 将 `EnableDetailedErrors` 设置为 `true` 可能会泄漏敏感信息。 | | CompressionProviders | gzip | 用于压缩和解压缩消息的压缩提供程序的集合。 可以创建自定义压缩提供程序并将其添加到集合中。 默认已配置提供程序支持 gzip 压缩。 | | <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | 用于压缩从服务器发送的消息的压缩算法。 该算法必须与 `CompressionProviders` 中的压缩提供程序匹配。 若要使算法可压缩响应，客户端必须通过在 grpc-accept-encoding 标头中进行发送来指示它支持算法。 | | ResponseCompressionLevel | `null` | 用于压缩从服务器发送的消息的压缩级别。 | | Interceptors | None | 随每个 gRPC 调用一起运行的侦听器的集合。 侦听器按注册顺序运行。 全局配置的侦听器在为单个服务配置的侦听器之前运行。 有关 gRPC 侦听器的详细信息，请参阅 [gRPC 侦听器与中间件](xref:grpc/migration#grpc-interceptors-vs-middleware)。 | | IgnoreUnknownServices | `false` | 如果为 `true`，则对未知服务和方法的调用不会返回 UNIMPLEMENTED 状态，并且请求会传递到 ASP.NET Core 中的下一个注册中间件。 |

可以通过在 `Startup.ConfigureServices` 中向 `AddGrpc` 调用提供选项委托，为所有服务配置选项：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

用于单个服务的选项会替代 `AddGrpc` 中提供的全局选项，可以使用 `AddServiceOptions<TService>` 进行配置：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>配置客户端选项

gRPC 客户端配置在 `GrpcChannelOptions` 中进行设置。 下表描述了用于配置 gRPC 通道的选项：

| 选项 | 默认值 | 描述 |
| ---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------ | | HttpHandler | New instance | 用于进行 gRPC 调用的 `HttpMessageHandler`。 可以将客户端设置为配置自定义 `HttpClientHandler`，或将附加处理程序添加到 gRPC 调用的 HTTP 管道。 如果未指定 `HttpMessageHandler`，则会通过自动处置为通道创建新 `HttpClientHandler` 实例。 | | HttpClient | `null` | 用于进行 gRPC 调用的 `HttpClient`。 此设置是 `HttpHandler` 的替代项。 | | DisposeHttpClient | `false` | 如果设置为 `true` 且指定了 `HttpMessageHandler` 或 `HttpClient`，则在处置 `GrpcChannel` 时，将分别处置 `HttpHandler` 或 `HttpClient`。 | | LoggerFactory | `null` | 客户端用于记录有关 gRPC 调用的信息的 `LoggerFactory`。 可以通过依赖项注入来解析或使用 `LoggerFactory.Create` 来创建 `LoggerFactory` 实例。 有关配置日志记录的示例，请参阅 <xref:grpc/diagnostics#grpc-client-logging>。 | | MaxSendMessageSize | `null` | 可以从客户端发送的最大消息大小（以字节为单位）。 尝试发送超过配置的最大消息大小的消息会导致异常。 设置为 `null`时，消息的大小不受限制。 | | <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | 可以由客户端接收的最大消息大小（以字节为单位）。 如果客户端收到的消息超过此限制，则会引发异常。 增大此值可使客户端接收更大的消息，但可能会对内存消耗产生负面影响。 设置为 `null`时，消息的大小不受限制。 | | Credentials | `null` | `ChannelCredentials` 实例。 凭据用于将身份验证元数据添加到 gRPC 调用。 | | CompressionProviders | gzip | 用于压缩和解压缩消息的压缩提供程序的集合。 可以创建自定义压缩提供程序并将其添加到集合中。 默认已配置提供程序支持 gzip 压缩。 |

下面的代码：

* 设置通道上发送和接收的最大消息大小。
* 创建客户端。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>其他资源

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
