<span data-ttu-id="4dd06-101">添加 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 以通过 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> 获取 `User.Read` 权限：</span><span class="sxs-lookup"><span data-stu-id="4dd06-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
