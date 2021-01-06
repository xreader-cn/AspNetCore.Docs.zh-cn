# <a name="blazor-webassembly-sample-app"></a><span data-ttu-id="3d4dd-101">Blazor WebAssembly 示例应用</span><span class="sxs-lookup"><span data-stu-id="3d4dd-101">Blazor WebAssembly Sample App</span></span>

<span data-ttu-id="3d4dd-102">此示例演示如何使用 Blazor 文档所述的 Blazor 方案。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="3d4dd-103">调用 Web API 示例</span><span class="sxs-lookup"><span data-stu-id="3d4dd-103">Call web API example</span></span>

<span data-ttu-id="3d4dd-104">Web API 示例需要一个基于示例应用运行的 Web API，以用于<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">使用 ASP.NET Core 创建 Web API</a> 主题，该主题默认使用与 Blazor 示例应用相同的 HTTPS 端口 (5001)。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Create a web API with ASP.NET Core</a> topic, which by default uses the same HTTPS port (5001) as the Blazor sample app.</span></span> <span data-ttu-id="3d4dd-105">若要同时在同一台计算机上使用两个应用，请更改 Web API 的端口（例如，使用端口 10000）。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-105">To use both apps on the same machine at the same time, change the port of the web API (for example, use port 10000).</span></span> <span data-ttu-id="3d4dd-106">示例应用通过 `https://localhost:10000/api/TodoItems` 向 Web API 发出请求。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-106">The sample app makes requests to the web API at `https://localhost:10000/api/TodoItems`.</span></span> <span data-ttu-id="3d4dd-107">如果使用其他 Web API 地址，请更新 Razor 组件的 `@code` 块中的 `ServiceEndpoint` 常量值。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-107">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@code` block.</span></span></p>

<span data-ttu-id="3d4dd-108">示例应用从 `http://localhost:5000` 或 `https://localhost:5001` 向 Web API 发出<a href="https://docs.microsoft.com/aspnet/core/security/cors">跨域资源共享 (CORS)</a> 请求。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-108">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="3d4dd-109">允许使用凭据（授权 cookie/标头）。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-109">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="3d4dd-110">将以下 CORS 中间件配置添加到 Web API 的 `Startup.Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="3d4dd-110">Add the following CORS middleware configuration to the web API's `Startup.Configure` method:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="3d4dd-111">根据 Blazor 应用的需要，调整 `WithOrigins` 的域和端口。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-111">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="3d4dd-112">已为 CORS 配置 Web API，以允许授权 cookie/标头和来自客户端代码的请求，但本教程创建的 Web API 实际并未授权请求。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-112">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="3d4dd-113">请参阅 <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 安全性和标识文章</a>获取实现指南。</span><span class="sxs-lookup"><span data-stu-id="3d4dd-113">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>
