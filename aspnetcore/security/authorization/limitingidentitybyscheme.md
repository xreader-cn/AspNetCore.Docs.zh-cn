---
title: 使用 ASP.NET Core 中的特定方案授权
author: rick-anderson
description: 本文介绍如何在使用多个身份验证方法时将标识限制为特定方案。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: a3be2b8171c146beef7e62c8f7e55883ca5dc687
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652980"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>使用 ASP.NET Core 中的特定方案授权

在某些情况下（例如单页应用程序（Spa）），通常使用多种身份验证方法。 例如，应用可能会使用基于 cookie 的身份验证来登录和 JWT 请求的 JWT 持有者身份验证。 在某些情况下，应用程序可能有多个身份验证处理程序实例。 例如，两个 cookie 处理程序，其中一个包含基本标识，一个在已触发多重身份验证（MFA）时创建。 可能会触发 MFA，因为用户请求了需要额外安全的操作。 有关在用户请求需要 MFA 的资源时强制执行 MFA 的详细信息，请参阅 GitHub 颁发[保护部分与 mfa](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)。

身份验证方案是在身份验证过程中配置身份验证服务时命名的。 例如：

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

在前面的代码中，添加了两个身份验证处理程序：一个用于 cookie，另一个用于持有者。

>[!NOTE]
>指定默认方案将导致设置为该标识的 `HttpContext.User` 属性。 如果不需要该行为，请通过调用 `AddAuthentication`的无参数形式来禁用它。

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>选择具有授权属性的方案

在授权时，应用指示要使用的处理程序。 通过向 `[Authorize]`传递以逗号分隔的身份验证方案列表，选择应用将授权的处理程序。 `[Authorize]` 属性指定要使用的身份验证方案或方案，不管是否配置了默认设置。 例如：

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

在前面的示例中，cookie 和持有者处理程序都运行，并且有机会为当前用户创建并追加标识。 通过仅指定一个方案，将运行相应的处理程序。

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

在前面的代码中，只有具有 "持有者" 方案的处理程序才会运行。 将忽略任何基于 cookie 的标识。

## <a name="selecting-the-scheme-with-policies"></a>选择具有策略的方案

如果希望在[策略](xref:security/authorization/policies)中指定所需的方案，则可以在添加策略时设置 `AuthenticationSchemes` 集合：

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

在前面的示例中，"Over18" 策略仅针对 "持有者" 处理程序创建的标识运行。 通过设置 `[Authorize]` 属性的 `Policy` 属性来使用该策略：

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>使用多种身份验证方案

某些应用可能需要支持多种身份验证类型。 例如，你的应用程序可以从 Azure Active Directory 和用户数据库对用户进行身份验证。 另一个示例是从 Active Directory 联合身份验证服务和 Azure Active Directory B2C 对用户进行身份验证的应用程序。 在这种情况下，应用程序应接受来自多个颁发者的 JWT 持有者令牌。

添加想要接受的所有身份验证方案。 例如，`Startup.ConfigureServices` 中的以下代码将添加两个具有不同颁发者的 JWT 持有者身份验证方案：

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
> 仅 `JwtBearerDefaults.AuthenticationScheme`的默认身份验证方案注册一个 JWT 持有者身份验证。 必须使用唯一的身份验证方案注册附加身份验证。

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

重写默认授权策略时，可以使用控制器中的 `[Authorize]` 属性。 然后，控制器接受由第一个或第二个颁发者颁发的 JWT 的请求。

::: moniker-end
