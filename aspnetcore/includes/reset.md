<span data-ttu-id="df0c2-101">通过“Reset”，服务器可以使用指定的错误代码重置 HTTP/2 请求。</span><span class="sxs-lookup"><span data-stu-id="df0c2-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="df0c2-102">重置请求被视为中止。</span><span class="sxs-lookup"><span data-stu-id="df0c2-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="df0c2-103">前述代码示例中的 `Reset` 指定 `INTERNAL_ERROR` 错误代码。</span><span class="sxs-lookup"><span data-stu-id="df0c2-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="df0c2-104">有关 HTTP/2 错误代码的详细信息，请访问[“HTTP/2 规范错误代码”部分](https://tools.ietf.org/html/rfc7540#page-50)。</span><span class="sxs-lookup"><span data-stu-id="df0c2-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>