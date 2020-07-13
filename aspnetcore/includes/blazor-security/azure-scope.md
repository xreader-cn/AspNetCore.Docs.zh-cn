> [!NOTE]
> 如果 Azure 门户为应用提供作用域 URI，并且当应用收到来自 API 的“401 未授权”响应时引发未处理的异常，则尝试使用不包括方案和宿主的作用域 URI。 例如，Azure 门户可以提供以下作用域 URI 格式之一：
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 尝试提供不包含方案和宿主的作用域 URI：
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> 例如：
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
