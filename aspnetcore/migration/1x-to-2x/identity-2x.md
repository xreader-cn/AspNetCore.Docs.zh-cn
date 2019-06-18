---
title: 将身份验证和标识迁移到 ASP.NET Core 2.0
author: scottaddie
description: 本文概述了迁移 ASP.NET Core 1.x 身份验证和标识为 ASP.NET Core 2.0 的最常见步骤。
ms.author: scaddie
ms.date: 06/13/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 3e8bc75b87a85159c9668b52eea32bb7d700be6c
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196372"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>将身份验证和标识迁移到 ASP.NET Core 2.0

通过[Scott Addie](https://github.com/scottaddie)和[Hao 永远](https://github.com/HaoK)

ASP.NET Core 2.0 具有新的模型进行身份验证和[标识](xref:security/authentication/identity)，通过使用服务可简化配置。 ASP.NET Core 1.x 应用程序使用身份验证或标识可以更新以使用新的模型，如下所述。

## <a name="update-namespaces"></a>更新命名空间

在 1.x 中，类等`IdentityRole`并`IdentityUser`中找到了`Microsoft.AspNetCore.Identity.EntityFrameworkCore`命名空间。

在 2.0 中，<xref:Microsoft.AspNetCore.Identity>命名空间成为多个这样的类的新的家。 使用默认标识代码中，受影响的类包括`ApplicationUser`和`Startup`。 调整您`using`语句解析受影响的引用。

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>身份验证中间件和服务

在 1.x 项目中，通过中间件配置身份验证。 中间件方法调用每个你想要支持的身份验证方案。

下面的 1.x 示例中的标识配置 Facebook 身份验证*Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

在 2.0 项目中，通过服务配置身份验证。 在中注册每个身份验证方案`ConfigureServices`方法*Startup.cs*。 `UseIdentity`方法替换`UseAuthentication`。

下面的 2.0 示例中的标识配置 Facebook 身份验证*Startup.cs*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

`UseAuthentication`方法将添加一个单一的身份验证中间件组件，负责进行自动身份验证和远程身份验证请求的处理。 它会替换所有单独的中间件组件共有的中间件组件。

下面是有关每个主要身份验证方案的 2.0 迁移说明。

### <a name="cookie-based-authentication"></a>基于 cookie 的身份验证

选择以下两个选项之一并进行必要的更改在*Startup.cs*:

1. 标识与使用 cookie
    - 替换`UseIdentity`与`UseAuthentication`中`Configure`方法：

        ```csharp
        app.UseAuthentication();
        ```

    - 调用`AddIdentity`中的方法`ConfigureServices`方法添加 cookie 身份验证服务。
    - （可选） 调用`ConfigureApplicationCookie`或`ConfigureExternalCookie`中的方法`ConfigureServices`标识 cookie 设置进行调整的方法。

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. 使用 cookie，而无需标识
    - 替换`UseCookieAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - 调用`AddAuthentication`并`AddCookie`中的方法`ConfigureServices`方法：

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a>JWT 持有者身份验证

进行以下更改中的*Startup.cs*:
- 替换`UseJwtBearerAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddJwtBearer`中的方法`ConfigureServices`方法：

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    此代码片段不使用标识，因此应通过将传递设置的默认方案`JwtBearerDefaults.AuthenticationScheme`到`AddAuthentication`方法。

### <a name="openid-connect-oidc-authentication"></a>OpenID Connect (OIDC) 身份验证

进行以下更改中的*Startup.cs*:

- 替换`UseOpenIdConnectAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddOpenIdConnect`中的方法`ConfigureServices`方法：

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- 替换`PostLogoutRedirectUri`中的属性`OpenIdConnectOptions`操作并提供`SignedOutRedirectUri`:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Facebook 身份验证

进行以下更改中的*Startup.cs*:
- 替换`UseFacebookAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddFacebook`中的方法`ConfigureServices`方法：

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Google 身份验证

进行以下更改中的*Startup.cs*:
- 替换`UseGoogleAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddGoogle`中的方法`ConfigureServices`方法：

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Microsoft 帐户身份验证

进行以下更改中的*Startup.cs*:
- 替换`UseMicrosoftAccountAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddMicrosoftAccount`中的方法`ConfigureServices`方法：

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Twitter 身份验证

进行以下更改中的*Startup.cs*:
- 替换`UseTwitterAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- 调用`AddTwitter`中的方法`ConfigureServices`方法：

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>设置默认身份验证方案

在 1.x 中，`AutomaticAuthenticate`并`AutomaticChallenge`的属性[AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基类用于设置单一身份验证方案。 强制实施此没有很好方法。

在 2.0 中，这两个属性已删除作为单个属性`AuthenticationOptions`实例。 它们可以在中配置`AddAuthentication`中的方法调用`ConfigureServices`方法*Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

在上述代码段中，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`("Cookie")。

或者，使用的重载的版本`AddAuthentication`方法设置多个属性。 在下面的重载的方法示例，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`。 身份验证方案可能还可以指定在个人`[Authorize]`属性或授权策略。

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

在 2.0 中定义默认方案，如果以下条件之一为 true:
- 您希望用户以进行自动签名
- 您使用`[Authorize]`而无需指定方案的属性或授权策略

此规则的例外是`AddIdentity`方法。 此方法将为你和设置默认值进行身份验证并为应用程序 cookie 质询方案添加 cookie `IdentityConstants.ApplicationScheme`。 此外，它将默认登录方案设置为外部 cookie `IdentityConstants.ExternalScheme`。

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>使用 HttpContext 身份验证扩展插件

`IAuthenticationManager`接口是 1.x 身份验证系统的主入口点。 它已替换为一组新的`HttpContext`中的扩展方法`Microsoft.AspNetCore.Authentication`命名空间。

例如，1.x 项目引用`Authentication`属性：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

在 2.0 项目中，导入`Microsoft.AspNetCore.Authentication`命名空间，并删除`Authentication`属性引用：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows 身份验证 (HTTP.sys / IISIntegration)

有两种变体的 Windows 身份验证：
1. 主机只允许经过身份验证的用户
2. 主机允许同时匿名和身份验证的用户

上面所述的第一种变化形式是不受 2.0 更改的影响。

2\.0 更改的情况下，会影响上面所述的第二个变体。 例如，您可能会允许匿名用户到你的应用在 IIS 或[HTTP.sys](xref:fundamentals/servers/httpsys)层在控制器级别但授权用户。 在此方案中，将默认方案设置为`IISDefaults.AuthenticationScheme`在`Startup.ConfigureServices`方法：

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

若要设置的默认方案的失败会阻止来自工作的挑战的授权请求。

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions 实例

2\.0 更改一个副作用是切换到使用名为选项而不是 cookie 选项实例。 删除自定义标识 cookie 方案名称的功能。

例如，1.x 项目使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)传递`IdentityCookieOptions`到参数*AccountController.cs*并*ManageController.cs*。 从提供的实例访问的外部 cookie 身份验证方案：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

前面提到的构造函数注入将成为在 2.0 项目中，不必要和`_externalCookieScheme`删除字段，可以：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1.x 项目中使用`_externalCookieScheme`字段，如下所示：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

在 2.0 项目中，将替换以下为前面的代码。 `IdentityConstants.ExternalScheme`常量可以直接使用。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

解决新添加`SignOutAsync`调用通过导入以下命名空间：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>添加 POCO IdentityUser 导航属性

Entity Framework (EF) Core 导航属性的基本`IdentityUser`POCO （普通旧 CLR 对象） 已被删除。 如果在 1.x 项目使用这些属性，手动将它们添加回 2.0 项目：

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

若要防止重复的外键，运行 EF Core 迁移时，将以下代码添加到你`IdentityDbContext`类的`OnModelCreating`方法 (后`base.OnModelCreating();`调用):

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a>替换为 GetExternalAuthenticationSchemes

同步方法`GetExternalAuthenticationSchemes`的异步版本，于是取消了。 在 1.x 项目具有以下代码*Controllers/ManageController.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

此方法将出现在*Views/Account/Login.cshtml*过：

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

在 2.0 项目中，使用<xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*>方法。 中的更改*ManageController.cs*类似于以下代码：

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

在中*Login.cshtml*，则`AuthenticationScheme`中访问属性`foreach`循环更改为`Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>ManageLoginsViewModel 属性更改

一个`ManageLoginsViewModel`中使用对象`ManageLogins`的操作*ManageController.cs*。 在 1.x 项目中，该对象的`OtherLogins`属性的返回类型是`IList<AuthenticationDescription>`。 此返回类型时需要导入`Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

在 2.0 项目中，返回类型更改为`IList<AuthenticationScheme>`。 此新的返回类型需要替换`Microsoft.AspNetCore.Http.Authentication`导入具有`Microsoft.AspNetCore.Authentication`导入。

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>其他资源

有关详细信息，请参阅[讨论身份验证 2.0](https://github.com/aspnet/Security/issues/1338) GitHub 上的问题。
