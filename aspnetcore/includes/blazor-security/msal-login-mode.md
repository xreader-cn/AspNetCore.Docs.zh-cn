<span data-ttu-id="510d5-101">框架默认为弹出式登录模式；如果无法打开弹出窗口，则回到重定向登录模式。</span><span class="sxs-lookup"><span data-stu-id="510d5-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="510d5-102">通过将 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的 `LoginMode` 属性设置为 `redirect`，将 MSAL 配置为使用重定向登录模式：</span><span class="sxs-lookup"><span data-stu-id="510d5-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="510d5-103">默认设置为 `popup`，字符串值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="510d5-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
