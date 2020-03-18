## <a name="multiple-authentication-providers"></a><span data-ttu-id="72ff2-101">多个身份验证提供程序</span><span class="sxs-lookup"><span data-stu-id="72ff2-101">Multiple authentication providers</span></span>

<span data-ttu-id="72ff2-102">如果应用需要多个提供程序，请在 [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication) 后面链接提供程序扩展方法：</span><span class="sxs-lookup"><span data-stu-id="72ff2-102">When the app requires multiple providers, chain the provider extension methods behind [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication):</span></span>

```csharp
services.AddAuthentication()
    .AddMicrosoftAccount(microsoftOptions => { ... })
    .AddGoogle(googleOptions => { ... })
    .AddTwitter(twitterOptions => { ... })
    .AddFacebook(facebookOptions => { ... });
```
