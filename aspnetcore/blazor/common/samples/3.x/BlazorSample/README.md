# <a name="blazor-client-side-sample-app"></a><span data-ttu-id="e2496-101">Blazor (客户端) 示例应用</span><span class="sxs-lookup"><span data-stu-id="e2496-101">Blazor (client-side) Sample App</span></span>

<span data-ttu-id="e2496-102">此示例演示如何使用 Blazor 文档中所述的 Blazor 方案。</span><span class="sxs-lookup"><span data-stu-id="e2496-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="e2496-103">调用 web API 示例</span><span class="sxs-lookup"><span data-stu-id="e2496-103">Call web API example</span></span>

<span data-ttu-id="e2496-104">Web api 示例需要基于<a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">教程的示例应用运行的 web api:使用 ASP.NET Core MVC</a>主题创建 web API。</span><span class="sxs-lookup"><span data-stu-id="e2496-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Tutorial: Create a web API with ASP.NET Core MVC</a> topic.</span></span> <span data-ttu-id="e2496-105">示例应用将请求发送到 web API `https://localhost:10000/api/todo`。</span><span class="sxs-lookup"><span data-stu-id="e2496-105">The sample app makes requests to the web API at `https://localhost:10000/api/todo`.</span></span> <span data-ttu-id="e2496-106">如果使用了不同的 web API 地址, 请更新`ServiceEndpoint` Razor 组件的`@functions`块中的常量值。</span><span class="sxs-lookup"><span data-stu-id="e2496-106">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@functions` block.</span></span></p>

<span data-ttu-id="e2496-107">该示例应用程序`https://localhost:5001`向 web API 发出<a href="https://docs.microsoft.com/aspnet/core/security/cors">跨域资源共享 (CORS)</a>请求`http://localhost:5000` 。</span><span class="sxs-lookup"><span data-stu-id="e2496-107">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="e2496-108">允许使用凭据 (授权 cookie/标头)。</span><span class="sxs-lookup"><span data-stu-id="e2496-108">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="e2496-109">在调用`Startup.Configure` `UseMvc`之前, 将以下 CORS 中间件配置添加到 web API 的方法:</span><span class="sxs-lookup"><span data-stu-id="e2496-109">Add the following CORS middleware configuration to the web API's `Startup.Configure` method before it calls `UseMvc`:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="e2496-110">`WithOrigins`根据需要调整 Blazor 应用的域和端口。</span><span class="sxs-lookup"><span data-stu-id="e2496-110">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="e2496-111">为 CORS 配置了 web API, 以允许来自客户端代码的授权 cookie/标头和请求, 但本教程创建的 web API 并未实际授权请求。</span><span class="sxs-lookup"><span data-stu-id="e2496-111">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="e2496-112">有关实现指南, 请参阅<a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core 安全和标识文章</a>。</span><span class="sxs-lookup"><span data-stu-id="e2496-112">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>
