框架默认为弹出式登录模式；如果无法打开弹出窗口，则回到重定向登录模式。 通过将 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的 `LoginMode` 属性设置为 `redirect`，将 MSAL 配置为使用重定向登录模式：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

默认设置为 `popup`，字符串值不区分大小写。
