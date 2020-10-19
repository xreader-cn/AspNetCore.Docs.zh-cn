<span data-ttu-id="df363-101">为 `openid` 和 `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> 添加一对 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>：</span><span class="sxs-lookup"><span data-stu-id="df363-101">Add a pair of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `openid` and `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("openid");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("offline_access");
});
```
