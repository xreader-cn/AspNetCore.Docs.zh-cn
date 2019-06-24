---
title: 将身份验证和标识迁移到 ASP.NET Core 2.0
author: scottaddie
description: 本文概述了迁移 ASP.NET Core 1.x 身份验证和标识为 ASP.NET Core 2.0 的最常见步骤。
ms.author: scaddie
ms.date: 06/21/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: c83356e12fa5ae581b369265b9d857b08445ed51
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313749"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="60929-103">将身份验证和标识迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="60929-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="60929-104">通过[Scott Addie](https://github.com/scottaddie)和[Hao 永远](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="60929-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="60929-105">ASP.NET Core 2.0 具有新的模型进行身份验证和[标识](xref:security/authentication/identity)，通过使用服务可简化配置。</span><span class="sxs-lookup"><span data-stu-id="60929-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="60929-106">ASP.NET Core 1.x 应用程序使用身份验证或标识可以更新以使用新的模型，如下所述。</span><span class="sxs-lookup"><span data-stu-id="60929-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="60929-107">更新命名空间</span><span class="sxs-lookup"><span data-stu-id="60929-107">Update namespaces</span></span>

<span data-ttu-id="60929-108">在 1.x 中，类等`IdentityRole`并`IdentityUser`中找到了`Microsoft.AspNetCore.Identity.EntityFrameworkCore`命名空间。</span><span class="sxs-lookup"><span data-stu-id="60929-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="60929-109">在 2.0 中，<xref:Microsoft.AspNetCore.Identity>命名空间成为多个这样的类的新的家。</span><span class="sxs-lookup"><span data-stu-id="60929-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="60929-110">使用默认标识代码中，受影响的类包括`ApplicationUser`和`Startup`。</span><span class="sxs-lookup"><span data-stu-id="60929-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="60929-111">调整您`using`语句解析受影响的引用。</span><span class="sxs-lookup"><span data-stu-id="60929-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="60929-112">身份验证中间件和服务</span><span class="sxs-lookup"><span data-stu-id="60929-112">Authentication Middleware and services</span></span>

<span data-ttu-id="60929-113">在 1.x 项目中，通过中间件配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="60929-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="60929-114">中间件方法调用每个你想要支持的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="60929-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="60929-115">下面的 1.x 示例中的标识配置 Facebook 身份验证*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="60929-116">在 2.0 项目中，通过服务配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="60929-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="60929-117">在中注册每个身份验证方案`ConfigureServices`方法*Startup.cs*。</span><span class="sxs-lookup"><span data-stu-id="60929-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="60929-118">`UseIdentity`方法替换`UseAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="60929-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="60929-119">下面的 2.0 示例中的标识配置 Facebook 身份验证*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="60929-120">`UseAuthentication`方法将添加一个单一的身份验证中间件组件，负责进行自动身份验证和远程身份验证请求的处理。</span><span class="sxs-lookup"><span data-stu-id="60929-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="60929-121">它会替换所有单独的中间件组件共有的中间件组件。</span><span class="sxs-lookup"><span data-stu-id="60929-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="60929-122">下面是有关每个主要身份验证方案的 2.0 迁移说明。</span><span class="sxs-lookup"><span data-stu-id="60929-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="60929-123">基于 cookie 的身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-123">Cookie-based authentication</span></span>

<span data-ttu-id="60929-124">选择以下两个选项之一并进行必要的更改在*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="60929-125">标识与使用 cookie</span><span class="sxs-lookup"><span data-stu-id="60929-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="60929-126">替换`UseIdentity`与`UseAuthentication`中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="60929-127">调用`AddIdentity`中的方法`ConfigureServices`方法添加 cookie 身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="60929-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="60929-128">（可选） 调用`ConfigureApplicationCookie`或`ConfigureExternalCookie`中的方法`ConfigureServices`标识 cookie 设置进行调整的方法。</span><span class="sxs-lookup"><span data-stu-id="60929-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="60929-129">使用 cookie，而无需标识</span><span class="sxs-lookup"><span data-stu-id="60929-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="60929-130">替换`UseCookieAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="60929-131">调用`AddAuthentication`并`AddCookie`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="60929-132">JWT 持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="60929-133">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="60929-134">替换`UseJwtBearerAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="60929-135">调用`AddJwtBearer`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="60929-136">此代码片段不使用标识，因此应通过将传递设置的默认方案`JwtBearerDefaults.AuthenticationScheme`到`AddAuthentication`方法。</span><span class="sxs-lookup"><span data-stu-id="60929-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="60929-137">OpenID Connect (OIDC) 身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="60929-138">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="60929-139">替换`UseOpenIdConnectAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="60929-140">调用`AddOpenIdConnect`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="60929-141">替换`PostLogoutRedirectUri`中的属性`OpenIdConnectOptions`操作并提供`SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="60929-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="60929-142">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-142">Facebook authentication</span></span>

<span data-ttu-id="60929-143">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="60929-144">替换`UseFacebookAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="60929-145">调用`AddFacebook`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="60929-146">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-146">Google authentication</span></span>

<span data-ttu-id="60929-147">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="60929-148">替换`UseGoogleAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="60929-149">调用`AddGoogle`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="60929-150">Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-150">Microsoft Account authentication</span></span>

<span data-ttu-id="60929-151">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-151">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="60929-152">替换`UseMicrosoftAccountAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-152">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="60929-153">调用`AddMicrosoftAccount`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-153">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="60929-154">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="60929-154">Twitter authentication</span></span>

<span data-ttu-id="60929-155">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-155">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="60929-156">替换`UseTwitterAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-156">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="60929-157">调用`AddTwitter`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="60929-157">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="60929-158">设置默认身份验证方案</span><span class="sxs-lookup"><span data-stu-id="60929-158">Setting default authentication schemes</span></span>

<span data-ttu-id="60929-159">在 1.x 中，`AutomaticAuthenticate`并`AutomaticChallenge`的属性[AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基类用于设置单一身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="60929-159">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="60929-160">强制实施此没有很好方法。</span><span class="sxs-lookup"><span data-stu-id="60929-160">There was no good way to enforce this.</span></span>

<span data-ttu-id="60929-161">在 2.0 中，这两个属性已删除作为单个属性`AuthenticationOptions`实例。</span><span class="sxs-lookup"><span data-stu-id="60929-161">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="60929-162">它们可以在中配置`AddAuthentication`中的方法调用`ConfigureServices`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-162">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="60929-163">在上述代码段中，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`("Cookie")。</span><span class="sxs-lookup"><span data-stu-id="60929-163">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="60929-164">或者，使用的重载的版本`AddAuthentication`方法设置多个属性。</span><span class="sxs-lookup"><span data-stu-id="60929-164">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="60929-165">在下面的重载的方法示例，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`。</span><span class="sxs-lookup"><span data-stu-id="60929-165">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="60929-166">身份验证方案可能还可以指定在个人`[Authorize]`属性或授权策略。</span><span class="sxs-lookup"><span data-stu-id="60929-166">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="60929-167">在 2.0 中定义默认方案，如果以下条件之一为 true:</span><span class="sxs-lookup"><span data-stu-id="60929-167">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="60929-168">您希望用户以进行自动签名</span><span class="sxs-lookup"><span data-stu-id="60929-168">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="60929-169">您使用`[Authorize]`而无需指定方案的属性或授权策略</span><span class="sxs-lookup"><span data-stu-id="60929-169">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="60929-170">此规则的例外是`AddIdentity`方法。</span><span class="sxs-lookup"><span data-stu-id="60929-170">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="60929-171">此方法将为你和设置默认值进行身份验证并为应用程序 cookie 质询方案添加 cookie `IdentityConstants.ApplicationScheme`。</span><span class="sxs-lookup"><span data-stu-id="60929-171">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="60929-172">此外，它将默认登录方案设置为外部 cookie `IdentityConstants.ExternalScheme`。</span><span class="sxs-lookup"><span data-stu-id="60929-172">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="60929-173">使用 HttpContext 身份验证扩展插件</span><span class="sxs-lookup"><span data-stu-id="60929-173">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="60929-174">`IAuthenticationManager`接口是 1.x 身份验证系统的主入口点。</span><span class="sxs-lookup"><span data-stu-id="60929-174">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="60929-175">它已替换为一组新的`HttpContext`中的扩展方法`Microsoft.AspNetCore.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="60929-175">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="60929-176">例如，1.x 项目引用`Authentication`属性：</span><span class="sxs-lookup"><span data-stu-id="60929-176">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="60929-177">在 2.0 项目中，导入`Microsoft.AspNetCore.Authentication`命名空间，并删除`Authentication`属性引用：</span><span class="sxs-lookup"><span data-stu-id="60929-177">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="60929-178">Windows 身份验证 (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="60929-178">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="60929-179">有两种变体的 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="60929-179">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="60929-180">主机仅允许经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="60929-180">The host only allows authenticated users.</span></span> <span data-ttu-id="60929-181">2\.0 的更改不影响此变体。</span><span class="sxs-lookup"><span data-stu-id="60929-181">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="60929-182">主机允许同时匿名和身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="60929-182">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="60929-183">2\.0 更改的情况下，会影响此变体。</span><span class="sxs-lookup"><span data-stu-id="60929-183">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="60929-184">例如，应用应允许匿名用户都[IIS](xref:host-and-deploy/iis/index)或[HTTP.sys](xref:fundamentals/servers/httpsys)层，但在控制器级别的用户授权。</span><span class="sxs-lookup"><span data-stu-id="60929-184">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="60929-185">在此方案中，在设置的默认方案`Startup.ConfigureServices`方法。</span><span class="sxs-lookup"><span data-stu-id="60929-185">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="60929-186">有关[Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)，设置为默认方案`IISDefaults.AuthenticationScheme`:</span><span class="sxs-lookup"><span data-stu-id="60929-186">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="60929-187">有关[Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)，设置为默认方案`HttpSysDefaults.AuthenticationScheme`:</span><span class="sxs-lookup"><span data-stu-id="60929-187">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="60929-188">未能设置的默认方案可以防止授权 （质询） 请求使用了以下异常：</span><span class="sxs-lookup"><span data-stu-id="60929-188">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="60929-189">`System.InvalidOperationException`：指定没有 authenticationScheme，并且不没有找到任何 DefaultChallengeScheme。</span><span class="sxs-lookup"><span data-stu-id="60929-189">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="60929-190">有关详细信息，请参阅 <xref:security/authentication/windowsauth>。</span><span class="sxs-lookup"><span data-stu-id="60929-190">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="60929-191">IdentityCookieOptions 实例</span><span class="sxs-lookup"><span data-stu-id="60929-191">IdentityCookieOptions instances</span></span>

<span data-ttu-id="60929-192">2\.0 更改一个副作用是切换到使用名为选项而不是 cookie 选项实例。</span><span class="sxs-lookup"><span data-stu-id="60929-192">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="60929-193">删除自定义标识 cookie 方案名称的功能。</span><span class="sxs-lookup"><span data-stu-id="60929-193">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="60929-194">例如，1.x 项目使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)传递`IdentityCookieOptions`到参数*AccountController.cs*并*ManageController.cs*。</span><span class="sxs-lookup"><span data-stu-id="60929-194">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="60929-195">从提供的实例访问的外部 cookie 身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="60929-195">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="60929-196">前面提到的构造函数注入将成为在 2.0 项目中，不必要和`_externalCookieScheme`删除字段，可以：</span><span class="sxs-lookup"><span data-stu-id="60929-196">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="60929-197">1.x 项目中使用`_externalCookieScheme`字段，如下所示：</span><span class="sxs-lookup"><span data-stu-id="60929-197">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="60929-198">在 2.0 项目中，将替换以下为前面的代码。</span><span class="sxs-lookup"><span data-stu-id="60929-198">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="60929-199">`IdentityConstants.ExternalScheme`常量可以直接使用。</span><span class="sxs-lookup"><span data-stu-id="60929-199">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="60929-200">解决新添加`SignOutAsync`调用通过导入以下命名空间：</span><span class="sxs-lookup"><span data-stu-id="60929-200">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="60929-201">添加 POCO IdentityUser 导航属性</span><span class="sxs-lookup"><span data-stu-id="60929-201">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="60929-202">Entity Framework (EF) Core 导航属性的基本`IdentityUser`POCO （普通旧 CLR 对象） 已被删除。</span><span class="sxs-lookup"><span data-stu-id="60929-202">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="60929-203">如果在 1.x 项目使用这些属性，手动将它们添加回 2.0 项目：</span><span class="sxs-lookup"><span data-stu-id="60929-203">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="60929-204">若要防止重复的外键，运行 EF Core 迁移时，将以下代码添加到你`IdentityDbContext`类的`OnModelCreating`方法 (后`base.OnModelCreating();`调用):</span><span class="sxs-lookup"><span data-stu-id="60929-204">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="60929-205">替换为 GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="60929-205">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="60929-206">同步方法`GetExternalAuthenticationSchemes`的异步版本，于是取消了。</span><span class="sxs-lookup"><span data-stu-id="60929-206">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="60929-207">在 1.x 项目具有以下代码*Controllers/ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="60929-207">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="60929-208">此方法将出现在*Views/Account/Login.cshtml*过：</span><span class="sxs-lookup"><span data-stu-id="60929-208">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="60929-209">在 2.0 项目中，使用<xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*>方法。</span><span class="sxs-lookup"><span data-stu-id="60929-209">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="60929-210">中的更改*ManageController.cs*类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="60929-210">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="60929-211">在中*Login.cshtml*，则`AuthenticationScheme`中访问属性`foreach`循环更改为`Name`:</span><span class="sxs-lookup"><span data-stu-id="60929-211">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="60929-212">ManageLoginsViewModel 属性更改</span><span class="sxs-lookup"><span data-stu-id="60929-212">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="60929-213">一个`ManageLoginsViewModel`中使用对象`ManageLogins`的操作*ManageController.cs*。</span><span class="sxs-lookup"><span data-stu-id="60929-213">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="60929-214">在 1.x 项目中，该对象的`OtherLogins`属性的返回类型是`IList<AuthenticationDescription>`。</span><span class="sxs-lookup"><span data-stu-id="60929-214">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="60929-215">此返回类型时需要导入`Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="60929-215">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="60929-216">在 2.0 项目中，返回类型更改为`IList<AuthenticationScheme>`。</span><span class="sxs-lookup"><span data-stu-id="60929-216">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="60929-217">此新的返回类型需要替换`Microsoft.AspNetCore.Http.Authentication`导入具有`Microsoft.AspNetCore.Authentication`导入。</span><span class="sxs-lookup"><span data-stu-id="60929-217">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="60929-218">其他资源</span><span class="sxs-lookup"><span data-stu-id="60929-218">Additional resources</span></span>

<span data-ttu-id="60929-219">有关详细信息，请参阅[讨论身份验证 2.0](https://github.com/aspnet/Security/issues/1338) GitHub 上的问题。</span><span class="sxs-lookup"><span data-stu-id="60929-219">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
