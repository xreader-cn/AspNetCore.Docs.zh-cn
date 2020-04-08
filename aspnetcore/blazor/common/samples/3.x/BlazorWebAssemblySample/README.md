# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly 示例应用

此示例演示如何使用 Blazor 文档所述的 Blazor 方案。

## <a name="call-web-api-example"></a>调用 Web API 示例

Web API 示例需要一个基于示例应用运行的 Web API，以用于<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">使用 ASP.NET Core 创建 Web API</a> 主题，该主题默认使用与 Blazor 示例应用相同的 HTTPS 端口 (5001)。 若要同时在同一台计算机上使用两个应用，请更改 Web API 的端口（例如，使用端口 10000）。 示例应用通过 `https://localhost:10000/api/TodoItems` 向 Web API 发出请求。 如果使用其他 Web API 地址，请更新 Razor 组件的 `ServiceEndpoint` 块中的 `@code` 常量值。</p>

示例应用从 <a href="https://docs.microsoft.com/aspnet/core/security/cors"> 或 </a> 向 Web API 发出`http://localhost:5000`跨域资源共享 (CORS)`https://localhost:5001` 请求。 允许使用凭据（授权 cookie/标头）。 将以下 CORS 中间件配置添加到 Web API 的 `Startup.Configure` 方法：</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

根据 Blazor 应用的需要，调整 `WithOrigins` 的域和端口。

已为 CORS 配置 Web API，以允许授权 cookie/标头和来自客户端代码的请求，但本教程创建的 Web API 实际并未授权请求。 请参阅 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 安全性和标识文章</a>获取实现指南。
