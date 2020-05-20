> [!NOTE]
> 如果 Azure 门户提供应用的作用域 URI，并且应用在收到来自 API 的*401 未经授权*的响应时引发未处理的异常，请尝试使用不包括方案和主机的范围 uri。 例如，Azure 门户可能提供以下作用域 URI 格式之一：
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 尝试提供不包含方案和主机的作用域 URI：
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
