> [!WARNING]
> Azure 应用服务或 IIS 当前不支持 [ASP.NET Core gRPC](xref:grpc/index)。 Http.Sys 的 HTTP/2 实现不支持 gRPC 依赖的 HTTP 响应尾随标头。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/9020)。
