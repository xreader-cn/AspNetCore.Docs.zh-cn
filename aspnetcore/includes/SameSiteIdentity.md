ASP.NET Core[标识](xref:security/authentication/identity)在很大程度上不受[SameSite cookie](xref:security/samesite)的影响，如或集成的高级方案除外 `IFrames` `OpenIdConnect` 。

使用时 `Identity` ，请***不要***添加任何 cookie 提供程序或调用，而 ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` 是执行此操作 `Identity` 。