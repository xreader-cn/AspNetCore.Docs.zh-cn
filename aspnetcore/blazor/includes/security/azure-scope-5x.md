<span data-ttu-id="b2809-101">当使用已通过 AAD 注册的服务器 API，并且应用的 AAD 注册在依赖于[未验证的发布者域](/azure/active-directory/develop/howto-configure-publisher-domain)的租户中时，服务器 API 应用的应用 ID URI 不是 `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`，而是采用 `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 格式。</span><span class="sxs-lookup"><span data-stu-id="b2809-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="b2809-102">如果是这种情况，则 `Client` 应用的 `Program.Main` (`Program.cs`) 中的默认访问令牌作用域如下所示：</span><span class="sxs-lookup"><span data-stu-id="b2809-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="b2809-103">若要为匹配的受众配置服务器 API 应用，请将 `Server` API 应用设置文件 (`appsettings.json`) 中的 `Audience` 设置为匹配 Azure 门户提供的应用受众：</span><span class="sxs-lookup"><span data-stu-id="b2809-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="b2809-104">在上面的配置中，`Audience` 值的末尾不包括默认作用域 `/{DEFAULT SCOPE}`。</span><span class="sxs-lookup"><span data-stu-id="b2809-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="b2809-105">示例：</span><span class="sxs-lookup"><span data-stu-id="b2809-105">Example:</span></span>

<span data-ttu-id="b2809-106">`Client` 应用的 `Program.Main` (`Program.cs`)：</span><span class="sxs-lookup"><span data-stu-id="b2809-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="b2809-107">通过匹配的受众 (`Audience`) 配置 `Server` API 应用设置文件 (`appsettings.json`)：</span><span class="sxs-lookup"><span data-stu-id="b2809-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="b2809-108">在前面的示例中，`Audience` 值的末尾不包括默认作用域 `/API.Access`。</span><span class="sxs-lookup"><span data-stu-id="b2809-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
