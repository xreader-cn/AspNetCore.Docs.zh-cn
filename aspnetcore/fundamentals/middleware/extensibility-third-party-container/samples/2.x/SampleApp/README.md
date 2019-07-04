# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core 中间件扩展性示例

本示例演示如何将 [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) 和 [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) 与第三方依赖项注入容器[简单注入器](https://simpleinjector.org)配合使用。 本示例演示 [Middleware activation with a third-party container in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/extensibility-third-party-container)（使用 ASP.NET Core 中的第三方容器激活中间件）中所述的功能。
