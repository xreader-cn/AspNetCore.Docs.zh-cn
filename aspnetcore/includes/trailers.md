<span data-ttu-id="36aef-101">HTTP 尾部类似于 HTTP 标头，只不过它是在发送响应正文后发送的。</span><span class="sxs-lookup"><span data-stu-id="36aef-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="36aef-102">IIS 和 HTTP.sys 仅支持 HTTP/2 响应尾部。</span><span class="sxs-lookup"><span data-stu-id="36aef-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="36aef-103">在前面的示例代码中：</span><span class="sxs-lookup"><span data-stu-id="36aef-103">In the preceding example code:</span></span>

* <span data-ttu-id="36aef-104">`SupportsTrailers` 确保响应支持尾部。</span><span class="sxs-lookup"><span data-stu-id="36aef-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="36aef-105">`DeclareTrailer` 将给定的尾部名称添加到 `Trailer` 响应头。</span><span class="sxs-lookup"><span data-stu-id="36aef-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="36aef-106">虽然并不是必须要声明响应尾部，但是建议这样做。</span><span class="sxs-lookup"><span data-stu-id="36aef-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="36aef-107">如果要调用 `DeclareTrailer`，则必须在发送响应标头之前进行此操作。</span><span class="sxs-lookup"><span data-stu-id="36aef-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="36aef-108">`AppendTrailer` 追加尾部。</span><span class="sxs-lookup"><span data-stu-id="36aef-108">`AppendTrailer` appends the trailer.</span></span>
