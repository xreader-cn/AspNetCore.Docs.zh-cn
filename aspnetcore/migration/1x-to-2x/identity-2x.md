---
title: 将身份验证和升级 Identity 到 ASP.NET Core 2。0
author: scottaddie
description: 本文概述迁移 ASP.NET Core 1.x 身份验证和 ASP.NET Core 2.0 的最常见步骤 Identity 。
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: dacf6fa7191f51f36b9ba65a90746a26f958fc03
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408664"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="f54bb-103">将身份验证和升级 Identity 到 ASP.NET Core 2。0</span><span class="sxs-lookup"><span data-stu-id="f54bb-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="f54bb-104">作者： [Scott Addie](https://github.com/scottaddie)和[Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="f54bb-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="f54bb-105">ASP.NET Core 2.0 具有新的身份验证模型，并 [Identity](xref:security/authentication/identity) 使用服务简化了配置。</span><span class="sxs-lookup"><span data-stu-id="f54bb-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="f54bb-106">ASP.NET Core 1.x 使用身份验证的应用程序或 Identity 可以更新为使用如下所述的新模型。</span><span class="sxs-lookup"><span data-stu-id="f54bb-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="f54bb-107">更新命名空间</span><span class="sxs-lookup"><span data-stu-id="f54bb-107">Update namespaces</span></span>

<span data-ttu-id="f54bb-108">在1.x 中，在 `IdentityRole` `IdentityUser` 命名空间中找到了类，如和 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="f54bb-109">在2.0 中， <xref:Microsoft.AspNetCore.Identity> 命名空间成为几个这样的类的新宿主。</span><span class="sxs-lookup"><span data-stu-id="f54bb-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="f54bb-110">对于默认 Identity 代码，受影响的类包括 `ApplicationUser` 和 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="f54bb-111">调整 `using` 语句以解析受影响的引用。</span><span class="sxs-lookup"><span data-stu-id="f54bb-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="f54bb-112">身份验证中间件和服务</span><span class="sxs-lookup"><span data-stu-id="f54bb-112">Authentication Middleware and services</span></span>

<span data-ttu-id="f54bb-113">在1.x 项目中，通过中间件配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="f54bb-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="f54bb-114">为要支持的每个身份验证方案调用中间件方法。</span><span class="sxs-lookup"><span data-stu-id="f54bb-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="f54bb-115">下面的1.x 示例 Identity 在*Startup.cs*中配置 Facebook 身份验证：</span><span class="sxs-lookup"><span data-stu-id="f54bb-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="f54bb-116">在2.0 项目中，通过服务配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="f54bb-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="f54bb-117">每个身份验证方案都是在 Startup.cs 的方法中注册的 `ConfigureServices` 。 *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f54bb-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="f54bb-118">此 `UseIdentity` 方法将替换为 `UseAuthentication` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="f54bb-119">以下2.0 示例 Identity 在*Startup.cs*中配置 Facebook 身份验证：</span><span class="sxs-lookup"><span data-stu-id="f54bb-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="f54bb-120">`UseAuthentication`方法添加单个身份验证中间件组件，该组件负责自动身份验证和远程身份验证请求的处理。</span><span class="sxs-lookup"><span data-stu-id="f54bb-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="f54bb-121">它将所有单个中间件组件替换为一个公共中间件组件。</span><span class="sxs-lookup"><span data-stu-id="f54bb-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="f54bb-122">下面是每个主要身份验证方案的2.0 迁移说明。</span><span class="sxs-lookup"><span data-stu-id="f54bb-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="f54bb-123">基于 Cookie 的身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-123">Cookie-based authentication</span></span>

<span data-ttu-id="f54bb-124">选择以下两个选项之一，并在*Startup.cs*中进行必要的更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="f54bb-125">使用 cookieIdentity</span><span class="sxs-lookup"><span data-stu-id="f54bb-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="f54bb-126">`UseIdentity` `UseAuthentication` 在方法中将替换为 `Configure` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="f54bb-127">`AddIdentity`在方法中调用方法 `ConfigureServices` 以添加 cookie 身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="f54bb-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="f54bb-128">还可以 `ConfigureApplicationCookie` `ConfigureExternalCookie` 在方法中调用或方法 `ConfigureServices` 来调整 Identity cookie 设置。</span><span class="sxs-lookup"><span data-stu-id="f54bb-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="f54bb-129">使用 cookie 而不Identity</span><span class="sxs-lookup"><span data-stu-id="f54bb-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="f54bb-130">`UseCookieAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="f54bb-131">`AddAuthentication` `AddCookie` 在方法中调用和方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="f54bb-132">JWT 持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="f54bb-133">在*Startup.cs*中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f54bb-134">`UseJwtBearerAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f54bb-135">`AddJwtBearer`在方法中调用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="f54bb-136">此代码段不使用 Identity ，因此应通过传递给方法来设置默认方案 `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="f54bb-137">OpenID Connect （OIDC）身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="f54bb-138">在*Startup.cs*中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="f54bb-139">`UseOpenIdConnectAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f54bb-140">`AddOpenIdConnect`在方法中调用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="f54bb-141">`PostLogoutRedirectUri`将操作中的属性替换为 `OpenIdConnectOptions` `SignedOutRedirectUri` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="f54bb-142">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-142">Facebook authentication</span></span>

<span data-ttu-id="f54bb-143">在*Startup.cs*中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f54bb-144">`UseFacebookAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f54bb-145">`AddFacebook`在方法中调用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="f54bb-146">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-146">Google authentication</span></span>

<span data-ttu-id="f54bb-147">在*Startup.cs*中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f54bb-148">`UseGoogleAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f54bb-149">`AddGoogle`在方法中调用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="f54bb-150">Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-150">Microsoft Account authentication</span></span>

<span data-ttu-id="f54bb-151">有关 Microsoft 帐户身份验证的详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/14455)。</span><span class="sxs-lookup"><span data-stu-id="f54bb-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="f54bb-152">在*Startup.cs*中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f54bb-153">`UseMicrosoftAccountAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f54bb-154">`AddMicrosoftAccount`在方法中调用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="f54bb-155">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="f54bb-155">Twitter authentication</span></span>

<span data-ttu-id="f54bb-156">在*Startup.cs*中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="f54bb-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f54bb-157">`UseTwitterAuthentication`将方法中的方法调用替换 `Configure` 为 `UseAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f54bb-158">`AddTwitter`在方法中调用方法 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="f54bb-159">设置默认身份验证方案</span><span class="sxs-lookup"><span data-stu-id="f54bb-159">Setting default authentication schemes</span></span>

<span data-ttu-id="f54bb-160">在1.x 中，将 `AutomaticAuthenticate` `AutomaticChallenge` 在单个身份验证方案中设置[AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基类的和属性。</span><span class="sxs-lookup"><span data-stu-id="f54bb-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="f54bb-161">没有正确的方法来强制执行此操作。</span><span class="sxs-lookup"><span data-stu-id="f54bb-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="f54bb-162">在2.0 中，这两个属性已作为各个实例的属性删除 `AuthenticationOptions` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="f54bb-163">可以在 Startup.cs 的方法中的方法调用中配置它们 `AddAuthentication` `ConfigureServices` ： *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f54bb-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="f54bb-164">在上面的代码段中，将默认方案设置为 `CookieAuthenticationDefaults.AuthenticationScheme` "（cookie）"。</span><span class="sxs-lookup"><span data-stu-id="f54bb-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f54bb-165">或者，使用方法的重载版本 `AddAuthentication` 设置多个属性。</span><span class="sxs-lookup"><span data-stu-id="f54bb-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="f54bb-166">在下面的重载方法示例中，将默认方案设置为 `CookieAuthenticationDefaults.AuthenticationScheme` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="f54bb-167">也可以在单个 `[Authorize]` 属性或授权策略中指定身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="f54bb-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="f54bb-168">如果满足以下条件之一，则定义2.0 中的默认方案：</span><span class="sxs-lookup"><span data-stu-id="f54bb-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="f54bb-169">希望用户自动登录</span><span class="sxs-lookup"><span data-stu-id="f54bb-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="f54bb-170">在 `[Authorize]` 不指定方案的情况下使用属性或授权策略</span><span class="sxs-lookup"><span data-stu-id="f54bb-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="f54bb-171">此规则的例外情况是 `AddIdentity` 方法。</span><span class="sxs-lookup"><span data-stu-id="f54bb-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="f54bb-172">此方法为你添加 cookie，并将默认身份验证和质询方案设置为应用程序 cookie `IdentityConstants.ApplicationScheme` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="f54bb-173">此外，它还将默认登录方案设置为外部 cookie `IdentityConstants.ExternalScheme` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="f54bb-174">使用 HttpContext 身份验证扩展</span><span class="sxs-lookup"><span data-stu-id="f54bb-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="f54bb-175">`IAuthenticationManager`接口是到1.x 身份验证系统的主要入口点。</span><span class="sxs-lookup"><span data-stu-id="f54bb-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="f54bb-176">它已替换为命名空间中的一组新 `HttpContext` 扩展方法 `Microsoft.AspNetCore.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="f54bb-177">例如，1.x 项目引用 `Authentication` 属性：</span><span class="sxs-lookup"><span data-stu-id="f54bb-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f54bb-178">在2.0 项目中，导入 `Microsoft.AspNetCore.Authentication` 命名空间，并删除 `Authentication` 属性引用：</span><span class="sxs-lookup"><span data-stu-id="f54bb-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="f54bb-179">Windows 身份验证（HTTP.sys/IISIntegration）</span><span class="sxs-lookup"><span data-stu-id="f54bb-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="f54bb-180">Windows 身份验证有两种变体：</span><span class="sxs-lookup"><span data-stu-id="f54bb-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="f54bb-181">该主机仅允许经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="f54bb-181">The host only allows authenticated users.</span></span> <span data-ttu-id="f54bb-182">此变体不受2.0 更改的影响。</span><span class="sxs-lookup"><span data-stu-id="f54bb-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="f54bb-183">宿主允许匿名用户和经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="f54bb-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="f54bb-184">此变体受2.0 更改的影响。</span><span class="sxs-lookup"><span data-stu-id="f54bb-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="f54bb-185">例如，应用程序应该允许[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)层上的匿名用户，但在控制器级别对用户进行授权。</span><span class="sxs-lookup"><span data-stu-id="f54bb-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="f54bb-186">在此方案中，在方法中设置默认方案 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="f54bb-187">对于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)，将默认方案设置为 `IISDefaults.AuthenticationScheme` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="f54bb-188">对于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)，将默认方案设置为 `HttpSysDefaults.AuthenticationScheme` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="f54bb-189">未能设置默认方案会阻止授权（质询）请求使用以下例外：</span><span class="sxs-lookup"><span data-stu-id="f54bb-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="f54bb-190">`System.InvalidOperationException`：未指定 authenticationScheme，且未找到 DefaultChallengeScheme。</span><span class="sxs-lookup"><span data-stu-id="f54bb-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="f54bb-191">有关详细信息，请参阅 <xref:security/authentication/windowsauth>。</span><span class="sxs-lookup"><span data-stu-id="f54bb-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="f54bb-192">IdentityCookieOptions 实例</span><span class="sxs-lookup"><span data-stu-id="f54bb-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="f54bb-193">2.0 更改的副作用是切换到使用命名选项而不是 cookie 选项实例。</span><span class="sxs-lookup"><span data-stu-id="f54bb-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="f54bb-194">将删除自定义 Identity cookie 方案名称的功能。</span><span class="sxs-lookup"><span data-stu-id="f54bb-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="f54bb-195">例如，1.x 项目使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)将 `IdentityCookieOptions` 参数传递到*AccountController.cs*和*ManageController.cs*中。</span><span class="sxs-lookup"><span data-stu-id="f54bb-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="f54bb-196">可从提供的实例中访问外部 cookie 身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="f54bb-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="f54bb-197">上述构造函数注入在2.0 项目中变得不必要， `_externalCookieScheme` 可以删除该字段：</span><span class="sxs-lookup"><span data-stu-id="f54bb-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="f54bb-198">1.x 项目使用此字段， `_externalCookieScheme` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="f54bb-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f54bb-199">在2.0 项目中，将前面的代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="f54bb-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="f54bb-200">`IdentityConstants.ExternalScheme`可以直接使用该常量。</span><span class="sxs-lookup"><span data-stu-id="f54bb-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f54bb-201">`SignOutAsync`通过导入以下命名空间来解析新添加的调用：</span><span class="sxs-lookup"><span data-stu-id="f54bb-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="f54bb-202">添加 IdentityUser POCO 导航属性</span><span class="sxs-lookup"><span data-stu-id="f54bb-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="f54bb-203">已 `IdentityUser` 删除基本 POCO （普通旧 CLR 对象）的实体框架（EF）核心导航属性。</span><span class="sxs-lookup"><span data-stu-id="f54bb-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="f54bb-204">如果你的1.x 项目使用这些属性，请将其手动添加回2.0 项目：</span><span class="sxs-lookup"><span data-stu-id="f54bb-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="f54bb-205">若要防止在运行 EF Core 迁移时出现重复的外键，请将以下内容添加到 `IdentityDbContext` 类的 `OnModelCreating` 方法（ `base.OnModelCreating();` 调用后）：</span><span class="sxs-lookup"><span data-stu-id="f54bb-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="f54bb-206">替换 GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="f54bb-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="f54bb-207">删除同步方法 `GetExternalAuthenticationSchemes` 是为了支持异步版本。</span><span class="sxs-lookup"><span data-stu-id="f54bb-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="f54bb-208">1.x 项目的*控制器/ManageController*中包含以下代码：</span><span class="sxs-lookup"><span data-stu-id="f54bb-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="f54bb-209">此方法也出现在*Views/Account/Login 中。 cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="f54bb-210">在2.0 项目中，请使用 <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> 方法。</span><span class="sxs-lookup"><span data-stu-id="f54bb-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="f54bb-211">*ManageController.cs*中的更改类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="f54bb-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="f54bb-212">在*Login*中， `AuthenticationScheme` 在循环中访问的属性 `foreach` 将更改为 `Name` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="f54bb-213">ManageLoginsViewModel 属性更改</span><span class="sxs-lookup"><span data-stu-id="f54bb-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="f54bb-214">`ManageLoginsViewModel`对象用于 `ManageLogins` *ManageController.cs*的操作。</span><span class="sxs-lookup"><span data-stu-id="f54bb-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="f54bb-215">在1.x 项目中，对象的 `OtherLogins` 属性返回类型为 `IList<AuthenticationDescription>` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="f54bb-216">此返回类型需要导入 `Microsoft.AspNetCore.Http.Authentication` ：</span><span class="sxs-lookup"><span data-stu-id="f54bb-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="f54bb-217">在2.0 项目中，返回类型更改为 `IList<AuthenticationScheme>` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="f54bb-218">这一新的返回类型需要 `Microsoft.AspNetCore.Http.Authentication` 使用导入替换导入 `Microsoft.AspNetCore.Authentication` 。</span><span class="sxs-lookup"><span data-stu-id="f54bb-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="f54bb-219">其他资源</span><span class="sxs-lookup"><span data-stu-id="f54bb-219">Additional resources</span></span>

<span data-ttu-id="f54bb-220">有关详细信息，请参阅 GitHub 上[的有关 Auth 2.0](https://github.com/aspnet/Security/issues/1338)问题的讨论。</span><span class="sxs-lookup"><span data-stu-id="f54bb-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
