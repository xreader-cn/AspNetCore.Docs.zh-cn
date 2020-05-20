> [!NOTE]
> <span data-ttu-id="1f02d-101">如果 Azure 门户提供应用的作用域 URI，并且应用在收到来自 API 的*401 未经授权*的响应时引发未处理的异常，请尝试使用不包括方案和主机的范围 uri。</span><span class="sxs-lookup"><span data-stu-id="1f02d-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1f02d-102">例如，Azure 门户可能提供以下作用域 URI 格式之一：</span><span class="sxs-lookup"><span data-stu-id="1f02d-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1f02d-103">尝试提供不包含方案和主机的作用域 URI：</span><span class="sxs-lookup"><span data-stu-id="1f02d-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
