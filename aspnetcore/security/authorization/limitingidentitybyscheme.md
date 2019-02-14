---
title: 使用 ASP.NET Core 中的特定方案授权
author: rick-anderson
description: 本文介绍如何使用多个身份验证方法时限制到特定方案的标识。
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248194"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>使用 ASP.NET Core 中的特定方案授权

在某些情况下，如单页面应用程序 (Spa) 是通常使用多个身份验证方法。 例如，应用可能会使用基于 cookie 的身份验证登录和 JWT 持有者身份验证的 JavaScript 请求。 在某些情况下，应用程序可能具有的身份验证处理程序的多个实例。 例如，其中一个包含基本身份标识的两个 cookie 处理程序，另一个时创建已触发多重身份验证 (MFA)。 用户请求需要额外的安全性的操作，因此，可能会触发 MFA。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

在身份验证过程中配置身份验证服务时，名为身份验证方案。 例如：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

已在前面的代码中，添加两个身份验证处理程序： 一个用于 cookie，一个为持有者。

>[!NOTE]
>指定的默认方案会导致`HttpContext.User`属性设置为该标识。 如果不需要该行为，请禁用调用的无参数形式`AddAuthentication`。

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

在身份验证过程配置身份验证中间件时，被命名为身份验证方案。 例如：

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
{
    // Code omitted for brevity

    app.UseCookieAuthentication(new CookieAuthenticationOptions()
    {
        AuthenticationScheme = "Cookie",
        LoginPath = "/Account/Unauthorized/",
        AccessDeniedPath = "/Account/Forbidden/",
        AutomaticAuthenticate = false
    });
    
    app.UseJwtBearerAuthentication(new JwtBearerOptions()
    {
        AuthenticationScheme = "Bearer",
        AutomaticAuthenticate = false,
        Audience = "http://localhost:5001/",
        Authority = "http://localhost:5000/",
        RequireHttpsMetadata = false
    });
```

已在前面的代码中，添加两个身份验证中间件： 分别用于 cookie 和持有者。

>[!NOTE]
>指定的默认方案会导致`HttpContext.User`属性设置为该标识。 如果不需要该行为，它通过设置来禁用`AuthenticationOptions.AutomaticAuthenticate`属性设置为`false`。

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>选择具有 Authorize 属性的使用方案

在授权，终端应用指示要使用的处理程序。 选择与应用程序将授权通过将传递到身份验证方案的以逗号分隔列表的处理程序`[Authorize]`。 `[Authorize]`特性指定身份验证方案或方案使用而不考虑是否配置默认值。 例如：

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

---

在前面的示例中，cookie 和持有者处理程序运行，并有机会创建并追加当前用户的标识。 通过指定一种方案，在运行相应的处理程序。

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

在上述代码中，仅具有"Bearer"方案处理程序运行。 任何基于 cookie 的标识将被忽略。

## <a name="selecting-the-scheme-with-policies"></a>选择使用策略的方案

如果想要指定在所需的方案[策略](xref:security/authorization/policies)，可以设置`AuthenticationSchemes`集合添加你的策略时：

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

在上述示例中，"Over18"策略只在运行针对"Bearer"处理程序创建的标识。 通过设置使用策略`[Authorize]`特性的`Policy`属性：

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>使用多个身份验证方案

某些应用可能需要支持多个类型的身份验证。 例如，您的应用程序可能会对用户从 Azure Active Directory 和用户数据库进行身份验证。 另一个示例是一个应用，用户从 Active Directory 联合身份验证服务和 Azure Active Directory B2C 进行身份验证。 在这种情况下，应用程序应接受来自多个颁发者的 JWT 持有者令牌。

添加你想要接受的所有身份验证方案。 例如，以下代码中`Startup.ConfigureServices`添加两个 JWT 持有者身份验证方案使用不同的颁发者：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> 只有一个 JWT 持有者身份验证注册的默认身份验证方案使用`JwtBearerDefaults.AuthenticationScheme`。 其他身份验证必须是唯一的身份验证方案使用其注册。

下一步是更新默认授权策略，以接受这两种身份验证方案。 例如：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

重写默认授权策略，因为它是可以使用`[Authorize]`控制器中的属性。 然后，该控制器使用由第一个或第二个颁发者颁发的 JWT 接受请求。

::: moniker-end
