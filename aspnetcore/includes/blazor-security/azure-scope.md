> [!NOTE]
> <span data-ttu-id="a1673-101">如果 Azure 门户为应用提供作用域 URI，并且当应用收到来自 API 的“401 未授权”响应时引发未处理的异常，则尝试使用不包括方案和宿主的作用域 URI。</span><span class="sxs-lookup"><span data-stu-id="a1673-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a1673-102">例如，Azure 门户可以提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="a1673-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a1673-103">尝试提供不包含方案和宿主的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="a1673-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> <span data-ttu-id="a1673-104">例如：</span><span class="sxs-lookup"><span data-stu-id="a1673-104">For example:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
