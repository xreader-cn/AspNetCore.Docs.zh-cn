# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly 示例应用

此示例演示如何使用 Blazor 文档中所述的 Blazor 方案。

## <a name="call-web-api-example"></a>调用 web API 示例

Web API 示例需要一个正在运行的 web API，该 API 基于用于<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">创建包含 ASP.NET Core 的 WEB api</a>的示例应用，该主题在默认情况下使用与 Blazor 示例应用相同的 HTTPS 端口（5001）。 若要同时在同一台计算机上使用这两个应用，请更改 web API 的端口（例如，使用端口10000）。 示例应用在 `https://localhost:10000/api/TodoItems` 向 web API 发出请求。 如果使用了不同的 web API 地址，请在 Razor 组件的 @no__t 块中更新 `ServiceEndpoint` 常数值。</p>

此示例应用将从 `http://localhost:5000` 或 `https://localhost:5001` 到 web API 的<a href="https://docs.microsoft.com/aspnet/core/security/cors">跨域资源共享（CORS）</a>请求。 允许使用凭据（授权 cookie/标头）。 将以下 CORS 中间件配置添加到 web API 的 `Startup.Configure` 方法：</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

根据 Blazor 应用的需要调整 @no__t 0 的域和端口。

为 CORS 配置了 web API，以允许来自客户端代码的授权 cookie/标头和请求，但本教程创建的 web API 并未实际授权请求。 有关实现指南，请参阅<a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 安全和标识文章</a>。
