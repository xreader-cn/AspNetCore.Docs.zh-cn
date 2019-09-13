# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly 示例应用

此示例演示如何使用 Blazor 文档中所述的 Blazor 方案。

## <a name="call-web-api-example"></a>调用 web API 示例

Web api 示例需要基于<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">教程的示例应用运行的 web api：使用 ASP.NET Core MVC</a>主题创建 web API。 示例应用将请求发送到 web API `https://localhost:10000/api/todo`。 如果使用了不同的 web API 地址，请更新`ServiceEndpoint` Razor 组件的`@functions`块中的常量值。</p>

该示例应用程序`https://localhost:5001`向 web API 发出<a href="https://docs.microsoft.com/aspnet/core/security/cors">跨域资源共享（CORS）</a>请求`http://localhost:5000` 。 允许使用凭据（授权 cookie/标头）。 在调用`Startup.Configure` `UseMvc`之前，将以下 CORS 中间件配置添加到 web API 的方法：</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

`WithOrigins`根据需要调整 Blazor 应用的域和端口。

为 CORS 配置了 web API，以允许来自客户端代码的授权 cookie/标头和请求，但本教程创建的 web API 并未实际授权请求。 有关实现指南，请参阅<a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 安全和标识文章</a>。
